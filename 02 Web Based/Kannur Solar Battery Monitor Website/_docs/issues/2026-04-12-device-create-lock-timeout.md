# Device Create API Lock Wait Timeout

Date: 2026-04-12  
Status: Resolved  
Area: Device creation transaction flow  
Impact: Bulk device creation (10+ devices) hangs for ~50 seconds then fails

---

## Summary

Creating devices in a loop (e.g., 10 POST requests to `/api/devices`) caused requests to hang for approximately 50 seconds, then return with `MySQL Error 1205: Lock wait timeout exceeded`.

The root cause was a **transaction boundary mismatch** in the device creation flow. A parent transaction was opened for the device + ownership write, but state history logging executed on a separate DB connection, causing it to wait on row locks and eventually timeout.

---

## Symptoms Observed

```
[50000.728ms] [rows:0] INSERT INTO `device_state_history` 
  (`device_id`,`caused_action`,`state_id`,`created_by`,`created_at`) 
  VALUES (32,1,5,2,'2026-04-12 19:53:47.422') RETURNING `id`

Error 1205 (HY000): Lock wait timeout exceeded; try restarting transaction
```

Stack trace:
```
github.com/aruncs31s/skvms/internal/service/device/writer.(*DeviceWriter).CreateDevice
  /internal/service/device/writer/writer.go:137
github.com/aruncs31s/skvms/internal/service.(*deviceService).CreateDevice
  /internal/service/device_service.go:223
```

Key observations:
- HTTP request stayed open for ~50 seconds (MySQL default lock wait timeout)
- The insert into `device_state_history` was the bottleneck
- The main device insert completed quickly, but history logging blocked
- Under concurrent load (10 devices), this cascaded into multiple timeouts

---

## Root Cause Analysis

### Transaction Flow (Before Fix)

**Service Layer** (`internal/service/device_service.go`, line 208–230):
```go
func (s *deviceService) CreateDevice(
    ctx context.Context,
    userID uint,
    req *dto.CreateDeviceRequest,
) (dto.DeviceWithType, error) {
    // ✓ Opens TX here
    tx := database.DB.WithContext(ctx).Begin()
    if tx.Error != nil {
        return dto.DeviceWithType{}, fmt.Errorf("failed to begin transaction: %w", tx.Error)
    }

    // Pass TX to writer
    newD, err := s.writer.CreateDevice(ctx, tx, userID, req)
    if err != nil {
        tx.Rollback()
        return dto.DeviceWithType{}, err
    }

    // Pass TX to ownership service
    if err := s.ownership.InitDeviceOwnership(ctx, tx, newD.ID, userID); err != nil {
        tx.Rollback()
        return dto.DeviceWithType{}, err
    }

    // ✓ Commits TX here (both device and ownership updates)
    if err := tx.Commit().Error; err != nil {
        return dto.DeviceWithType{}, err
    }

    return newD, nil
}
```

**Writer Layer** (`internal/service/device/writer/writer.go`, line 54–140):
```go
func (w *DeviceWriter) CreateDevice(
    ctx context.Context,
    tx *gorm.DB,
    userID uint,
    req *dto.CreateDeviceRequest,
) (dto.DeviceWithType, error) {
    // ... validation code ...

    newDevice, err := w.repo.CreateDevice(ctx, tx, device, details, assignment)
    if err != nil {
        logger.GetLogger().Error("error creating device", ...)
        return dto.DeviceWithType{}, err
    }

    // ✗ BUG: Log called WITHOUT tx parameter
    if err := w.stateHistoryService.Log(ctx, newDevice.ID, model.ActionCreate, 5, userID); err {
        logger.GetLogger().Error("error logging device state history after creating device", ...)
    }

    return w.mapDeviceToDeviceWithType(*device), nil
}
```

### Why This Causes a Lock Timeout

1. **Main TX (TX1)** starts in service layer, inserts device + ownership records
2. **TX1 holds** implicit write locks on the device rows
3. **History Log Call** executes on a **different TX (TX2)** (default DB connection)
4. **TX2** tries to insert into `device_state_history`
5. `device_state_history` has a **foreign key constraint** on `device_id`
6. **TX2 must acquire a lock** on the referenced device row to validate the constraint
7. **TX1 holds that lock** but hasn't released it yet (still in transaction)
8. **TX2 waits** for TX1 to release the lock → waits up to 50 seconds (MySQL default)
9. Timeout occurs → Error 1205

### Why It Fails Under Concurrent Load

With 10 simultaneous device creates:
- **TX1, TX2, TX3... TX10** each hold locks on their respective device rows
- **Multiple history inserts** (from separate connections) queue up waiting on locks
- Lock contention increases exponentially
- All 10 requests eventually timeout

---

## The Fix

Pass the same transaction object to history logging to ensure all writes happen in a single atomic operation.

### Change 1: `CreateDevice` (line 136)

**Before:**
```go
if err := w.stateHistoryService.Log(ctx, newDevice.ID, model.ActionCreate, 5, userID); err != nil {
```

**After:**
```go
if err := w.stateHistoryService.Log(ctx, newDevice.ID, model.ActionCreate, 5, userID, tx); err != nil {
```

### Change 2: `CreateMicrocontrollerDevice` (line 304)

**Before:**
```go
if err := w.stateHistoryService.Log(ctx, device.ID, model.ActionCreate, 5, userID); err != nil {
```

**After:**
```go
if err := w.stateHistoryService.Log(ctx, device.ID, model.ActionCreate, 5, userID, tx); err != nil {
```

### How the `Log` Method Handles Multiple DB Arguments

From `internal/service/device_state_history_service.go`, line 38–57:
```go
func (s *deviceStateHistoryService) Log(
    ctx context.Context,
    deviceID uint,
    action model.DeviceAction,
    newStateID uint,
    userID uint,
    db ...*gorm.DB,  // Variadic: accepts 0 or more *gorm.DB
) error {
    history := &model.DeviceStateHistory{
        DeviceID:     deviceID,
        CausedAction: action,
        StateID:      newStateID,
        CreatedBy:    userID,
    }
    
    // If a TX is provided, use it; otherwise use the default DB
    if len(db) > 0 && db[0] != nil {
        return s.repo.Create(ctx, db[0], history)  // Use passed TX
    }
    return s.repo.Create(ctx, nil, history)  // Use default connection
}
```

The method already supported transaction reuse via a variadic parameter; it just wasn't being called with the transaction.

---

## Transaction Flow (After Fix)

```
CreateDevice (Service)
├─ TX1 = db.Begin()
├─ writer.CreateDevice(ctx, TX1, ...)
│  ├─ repo.CreateDevice(ctx, TX1, ...)        # Uses TX1
│  └─ stateHistoryService.Log(..., TX1)       # ✓ NOW uses TX1
└─ ownership.InitDeviceOwnership(ctx, TX1, ...) # Uses TX1
└─ TX1.Commit()                                 # Single atomic commit
```

Now:
- Device insert → TX1
- Ownership insert → TX1
- State history insert → TX1
- All protected by the same lock scope
- No cross-transaction blocking

---

## Verification

### Test Case: Load Test

```python
def test_create_10_devices(self, token):
    import requests
    from time import time
    
    for i in range(10):
        payload = {"name": f"Device {i+1}", "type": 1}
        start = time()
        resp = requests.post(
            "http://localhost:8080/api/devices",
            json=payload,
            headers={"Authorization": f"Bearer {token}"}
        )
        elapsed = time() - start
        print(f"Device {i+1}: {resp.status_code} in {elapsed:.2f}s")
        assert resp.status_code == 201
        assert elapsed < 2.0  # Should complete in <2s, not 50s
```

### Expected Behavior (Post-Fix)

- Each request returns HTTP 201 within 1–2 seconds
- No "Lock wait timeout exceeded" errors in logs
- No long-running INSERT statements

### Log Before Fix
```
[50000.728ms] [rows:0] INSERT INTO `device_state_history` ... RETURNING `id`
Error 1205 (HY000): Lock wait timeout exceeded; try restarting transaction
```

### Log After Fix
```
[15.234ms] [rows:1] INSERT INTO `device_state_history` ... RETURNING `id`
[GIN] 201 | 67.609ms | CREATE /api/devices
```

---

## Related Code Locations

| File | Purpose |
|------|---------|
| `internal/service/device_service.go:208–230` | Orchestrates transaction, calls writer + ownership |
| `internal/service/device/writer/writer.go:54–140` | Creates device, logs history (NOW passes tx) |
| `internal/service/device_state_history_service.go:38–57` | Accepts optional tx parameter |
| `internal/repository/device_state_history_repository.go:24–34` | History record creation |

---

## Notes

- The Gin 307 redirect (`/api/devices → /api/devices/`) is a separate routing issue and unrelated to the lock timeout
- Similar pattern may exist in other multi-step operations; review other service methods that call transactional operations without passing the TX
- Consider adding a lint rule or code review checklist: *"If a service layer opens a transaction, all repository calls in that flow must reuse it"*
