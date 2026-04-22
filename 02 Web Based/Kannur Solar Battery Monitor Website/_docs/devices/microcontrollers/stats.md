---
title: Microcontroller Stats
tags:
  - api
  - microcontrollers
  - stats
---

# Microcontroller Stats

> [!tip] Endpoint
> - **URL:** `/api/devices/microcontrollers/stats`
> - **Method:** `GET`
> - **Auth:** Required (Admin)

Get statistics for all microcontrollers in the system.

## Response

```json
{
  "total_devices": 50,
  "online_devices": 42,
  "offline_devices": 8
}
```

| Field | Type | Description |
|-------|------|-------------|
| `total_devices` | int64 | Total microcontrollers |
| `online_devices` | int64 | Currently online |
| `offline_devices` | int64 | Currently offline |

| Status | Description |
|:------:|-------------|
| 200 | Success |
| 401 | Unauthorized |
| 403 | Forbidden (non-admin) |
| 500 | Server error |