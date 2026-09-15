---
title: My Device Stats
tags:
  - api
  - devices
  - stats
---

# My Device Stats

> [!tip] Endpoint
> - **URL:** `/api/devices/my/stats`
> - **Method:** `GET`
> - **Auth:** Required (JWT)

Get device statistics for the currently authenticated user.

## Response

```json
{
  "total_devices": 10,
  "online_devices": 8,
  "offline_devices": 2,
  "active_devices": 7,
  "inactive_devices": 3
}
```

| Field | Type | Description |
|-------|------|-------------|
| `total_devices` | int | Total devices owned by user |
| `online_devices` | int | Devices currently online |
| `offline_devices` | int | Devices currently offline |
| `active_devices` | int | Devices in active state |
| `inactive_devices` | int | Devices in inactive state |

| Status | Description |
|:------:|-------------|
| 200 | Success |
| 401 | Unauthorized |
| 500 | Server error |

> [!note] Admin Override
> If the user is an admin, returns system-wide stats instead of user-specific.