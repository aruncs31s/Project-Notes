---
title: My Devices
tags:
  - api
  - devices
  - my
---

# My Devices

> [!tip] Endpoint
> - **URL:** `/api/devices/my`
> - **Method:** `GET`
> - **Auth:** Required (JWT)

Get devices owned by the currently authenticated user.

## Query Parameters

| Parameter | Type | Description |
|-----------|------|-------------|
| `limit` | int | Number of results (default: 20) |
| `offset` | int | Pagination offset |

## Response

```json
{
  "devices": [
    {
      "id": 1,
      "name": "My ESP32 Device",
      "device_type": 2,
      "current_state": 1,
      "created_at": "2024-01-01T00:00:00Z"
    }
  ]
}
```

| Status | Description |
|:------:|-------------|
| 200 | Success |
| 401 | Unauthorized |
| 500 | Server error |

> [!note] Filtered by User
> Only returns devices created by or owned by the authenticated user.