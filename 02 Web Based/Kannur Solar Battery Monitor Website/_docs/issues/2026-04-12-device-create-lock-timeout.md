# Device Create API Lock Wait Timeout

Date: 2026-04-12
Status: Resolved
Area: Device creation transaction flow

## Summary
Creating devices in a loop (for example 10 POST requests to /api/devices) appeared to hang. The request eventually failed with MySQL error 1205 (Lock wait timeout exceeded), and the response took about 50 seconds before returning.

## Symptoms
- API request looked stuck during device creation.
- Logs showed a long-running insert into device_state_history.
- Error observed:
  - Error 1205 (HY000): Lock wait timeout exceeded; try restarting transaction
- The failing statement was an insert into device_state_history after device creation.

## Root Cause
The device creation flow started a transaction in the service layer, but state history logging was called without passing that transaction object.

That caused state history writes to run on a different DB connection/transaction than the main device insert and ownership initialization. Under load or repeated writes, this can block on locks and wait until timeout.

## Why it looked like an API hang
MySQL lock wait timeout is typically around 50 seconds, so the HTTP request stayed open while waiting for the lock and then failed, which appeared as a stuck API call.

## Fix Applied
State history logging now uses the same transaction (tx) used by the device creation flow.

Updated call sites:
- internal/service/device/writer/writer.go
  - CreateDevice: pass tx into stateHistoryService.Log(..., tx)
  - CreateMicrocontrollerDevice: pass tx into stateHistoryService.Log(..., tx)

## Result
Device creation no longer waits on a separate transaction for state history inserts, which removes the lock-wait timeout path in this flow.

## Verification Steps
1. Start API server.
2. Run repeated creates (example: 10 device POSTs in a loop).
3. Confirm each call returns 201 quickly.
4. Confirm no lock wait timeout errors in logs.

## Notes
- The Gin 307 redirect in logs (/api/devices -> /api/devices/) is unrelated to the lock timeout issue, though normalizing route usage can avoid extra redirects.
