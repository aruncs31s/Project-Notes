---
title: My Microcontroller Stats
tags:
  - api
  - microcontrollers
  - stats
---

# My Microcontroller Stats

> [!tip] Endpoint
> - **URL:** `/api/devices/microcontrollers/my/stats`
> - **Method:** `GET`
> - **Auth:** Required (JWT)

Get statistics for microcontrollers owned by the authenticated user.

## Response

```json
{
  "total_devices": 5,
  "online_devices": 4,
  "offline_devices": 1
}
```

| Field | Type | Description |
|-------|------|-------------|
| `total_devices` | int64 | User's microcontrollers |
| `online_devices` | int64 | Currently online |
| `offline_devices` | int64 | Currently offline |

| Status | Description |
|:------:|-------------|
| 200 | Success |
| 401 | Unauthorized |
| 500 | Server error |