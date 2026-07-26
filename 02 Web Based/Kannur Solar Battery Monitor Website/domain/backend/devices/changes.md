# Device Repository Normalization - Change Log

Date: 2026-04-15

## Scope
This document captures the full set of code changes present in this run for device-repository normalization and related compatibility updates.

Primary goal:
- Consolidate list/read flows to a single repository list method.
- Reuse that single list path from service and solar flows wherever possible.
- Keep non-list behavior available through compatibility methods while migration is in progress.

## Current Validation Status
The latest validation run in this workspace currently fails to build due to recent edits in service wiring.

Command:
```bash
go test ./...
```

Current errors:
```text
internal/service/device_service.go:241:3: cannot use func() *uint {…}() (value of type *uint) as []uint value in argument to s.reader.ListMicrocontrollerDevices
internal/service/device_service.go:494:3: cannot use &userID (value of type *uint) as []uint value in argument to s.reader.ListMicrocontrollerDevices
```

Notes:
- Earlier in the refactor cycle this path was compiling, but the latest workspace state has drifted and needs a final type alignment pass.

## File-Level Change Inventory

Modified:
- API_DEVICE_USER_AUTH.md
- internal/dto/device.go
- internal/dto/solar.go
- internal/handler/http/device/reader/my.go
- internal/handler/http/device/reader/stats.go
- internal/model/device.go
- internal/repository/device/reader/reader.go
- internal/repository/device_cache.go
- internal/repository/device_repository.go
- internal/repository/solar_repository.go
- internal/router/devices.go
- internal/service/device/reader/reader.go
- internal/service/device/writer/writer.go
- internal/service/device_auth_service.go
- internal/service/device_service.go
- internal/service/location_service.go
- internal/service/solar_service.go
- internal/service/user_service.go

Deleted:
- internal/repository/device/reader/stats.go
- logs/app.log

Added:
- generate_device_cache.py
- internal/service/filters/device.go

## Major Architecture Changes

### 1) Unified list/read contract in repository
The repository now uses a normalized reader contract:

```go
List(ctx context.Context,
    deviceType []model.HardwareType,
    owner []uint,
    limit, offset int,
    recent bool,
) ([]model.Device, int64, error)

Get(ctx context.Context, id uint) (model.Device, error)
```

This replaced scattered specialized list methods for most read flows.

### 2) Reader implementation moved to model-first results
Repository reader now loads model.Device records with preloads and optional joins for owner/type filters, then service layer maps to DTOs.

Key behavior:
- Optional device type filter via JOIN device_types.
- Optional owner filter via JOIN device_ownerships.
- Count query + paged fetch.
- Optional ordering for recent mode.
- Preloads for DeviceType, Version, Details, DeviceState.

Snippet:
```go
query := r.db.WithContext(ctx).Model(&model.Device{})

if deviceType != nil {
    query = query.Joins("JOIN device_types dt ON dt.id = devices.device_type").Where(
        "dt.hardware_type IN ?", deviceType,
    )
}
if owner != nil {
    query = query.Joins("JOIN device_ownerships do ON do.device_id = devices.id").Where(
        "do.owner_user_id IN ?", owner,
    )
}

err := query.Count(&total).Error
...
query = query.
    Preload("DeviceType").
    Preload("Version").
    Preload("Details").
    Preload("DeviceState").
    Limit(limit).
    Offset(offset)
```

### 3) Service read flows now route through the unified List
Device service reader now uses a single list path plus a listAll helper for full scans when needed.

Snippet:
```go
func (r *DeviceReader) List(ctx context.Context, f *filters.DeviceFilter) ([]dto.DeviceWithType, int64, error) {
    if f == nil {
        f = (&filters.DeviceFilter{}).Default()
    }

    devices, count, err := r.repo.List(ctx, f.Types, f.Owner, f.Limit, f.Offset, f.Recent)
    if err != nil || len(devices) == 0 {
        return []dto.DeviceWithType{}, 0, err
    }

    var result []dto.DeviceWithType
    for _, device := range devices {
        result = append(result, r.mapDeviceToDeviceWithType(device))
    }

    return result, count, nil
}
```

Derived methods migrated to List-based behavior include:
- ListRecentDevices
- ListDevicesByUser
- SearchMicrocontrollers
- SearchSensors
- ListAllSensors
- GetRecentlyCreatedDevices
- GetTotalDeviceCount
- GetOfflineDevices

### 4) Device filter model extracted and expanded
New shared filter object:

```go
type DeviceFilter struct {
    Devices []uint
    Owner   []uint
    Types   []model.HardwareType
    Recent  bool
    Parent  *[]uint
    Limit   int
    Offset  int
}
```

Important migration detail:
- Owner changed from pointer form to slice form in many callers.

Examples:
```go
Owner: []uint{userID}
Types: []model.HardwareType{model.HardwareTypeMicroController}
```

### 5) DTO shape normalized with embedded base device
A reusable DTO base was introduced:

```go
type Device struct {
    ID     uint   `json:"id"`
    Name   string `json:"name"`
    Status string `json:"status"`
}
```

Then embedded into view DTOs:

```go
type DeviceWithType struct {
    Device
    Type            string
    HardwareType    model.HardwareType
    IPAddress       string
    MACAddress      string
    FirmwareVersion string
    VersionID       uint
}
```

```go
type SolarDeviceWithType struct {
    Device
    Address           string
    City              string
    ChargingCurrent   float64
    BatteryVoltage    float64
    RemainingTime     float64
    LedStatus         string
    ConnectedDeviceIP string
}
```

This required mapping updates in:
- service/device/writer
- service/location_service
- service/solar_service
- handler/device/reader

### 6) Device status helper added to model
Status mapping was centralized:

```go
func (d *Device) Status() string {
    state := DeviceStateID(d.CurrentState)
    switch state {
    case ActiveState:
        return Active
    case InactiveState:
        return Inactive
    case MaintenanceState:
        return Maintenance
    case DecommissionedState:
        return Decommissioned
    case InitializedState:
        return Initialized
    default:
        return "unknown"
    }
}
```

This is used by service mappings after moving to model.Device list responses.

## Compatibility Layer and Transitional Methods

### Device repository compatibility
Although list logic was normalized, the repository still exposes non-list methods needed by write/control/solar/location/auth paths.

Examples retained/implemented:
- CreateDevice
- UpdateDevice
- DeleteDevice
- GetDevice
- FindVersionByID
- AddConnectedDevice / RemoveConnectedDevice / IsParent
- GetConnectedDevices
- GetConnectedDevicesByIDs
- GetDeviceStats
- GetDevicesByLocationID
- Count / CountActive

### Cached repository alignment
Cache wrapper now supports the normalized List signature and invalidates for mutating calls.

List key now includes filter dimensions:

```go
func keyDeviceList(deviceType []model.HardwareType, owner []uint, limit, offset int, recent bool) string {
    sort.Slice(deviceType, func(i, j int) bool { return deviceType[i] < deviceType[j] })
    sort.Slice(owner, func(i, j int) bool { return owner[i] < owner[j] })
    return fmt.Sprintf("devices:list:dt=%v:owner=%v:l=%d:o=%d:r=%t", deviceType, owner, limit, offset, recent)
}
```

## Solar Repository Migration
Solar list methods now rely on paginated calls to deviceRepo.List instead of dedicated type-specific repository methods.

Snippet:
```go
func (r *solarRepository) listSolarDevices(ctx context.Context, owner *uint) ([]model.Device, error) {
    const pageSize = 200
    var owners []uint
    if owner != nil {
        owners = []uint{*owner}
    }

    all := make([]model.Device, 0)
    offset := 0
    for {
        devices, total, err := r.deviceRepo.List(
            ctx,
            []model.HardwareType{model.HardwareTypeSolar},
            owners,
            pageSize,
            offset,
            false,
        )
        if err != nil {
            return nil, err
        }
        if len(devices) == 0 {
            break
        }
        all = append(all, devices...)
        offset += len(devices)
        if int64(offset) >= total {
            break
        }
    }
    return all, nil
}
```

Solar DTO mapping now uses embedded Device:

```go
solarDeviceWithType.Device = dto.Device{
    ID:     device.ID,
    Name:   device.Name,
    Status: device.DeviceState.Name,
}
```

## Handler and Service Call-Site Updates

Examples of call-site alignment:

```go
// handler: /devices/my
Owner: []uint{uid}
```

```go
// handler: /devices/microcontrollers/my
devices, count, err := h.deviceService.GetMC(ctx, &service.DeviceFilter{
    Owner:  []uint{userID},
    Types:  []model.HardwareType{model.HardwareTypeMicroController},
    Limit:  limit,
    Offset: offset,
})
```

```go
// auth service
device, err := s.deviceRepo.Get(ctx, deviceID)
if err != nil || device.ID == 0 {
    return "", fmt.Errorf("device not found or error: %w", err)
}
```

## API Documentation Update
API docs were expanded with ownership/privacy endpoints:
- GET /api/devices/:id/ownership
- POST /api/devices/:id/transfer
- PUT /api/devices/:id/public
- GET /api/devices/:id/transfer-history

## Transitional Artifacts

### Deleted stub
- internal/repository/device/reader/stats.go removed.

### Added helper script
- generate_device_cache.py added for cache wrapper generation experiments.

### Large commented legacy blocks
Both of these files currently contain extensive commented legacy code:
- internal/repository/device_repository.go
- internal/repository/device_cache.go

These comments preserve old implementations but make the files heavier and harder to scan.

## Follow-up Tasks Suggested

1. Fix current build errors in internal/service/device_service.go by aligning ListMicrocontrollerDevices owner argument type with []uint.
2. Decide whether to keep or remove the large commented legacy sections in repository files.
3. Add focused tests for List filter combinations:
   - type-only
   - owner-only
   - type+owner
   - recent ordering
   - pagination boundaries
4. Validate solar endpoints behavior after list-based migration.

## Quick Summary
- Core objective achieved structurally: list/read flows were normalized around a shared List method.
- DTOs and filters were modernized to support that model.
- Compatibility methods were retained so non-list workflows continue to function.
- Current workspace state needs one more compile-fix pass for microcontroller list owner argument types in device service.
