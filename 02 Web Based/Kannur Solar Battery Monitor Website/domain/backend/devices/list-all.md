---
title: List All Devices
tags:
  - api
  - devices
  - list
---

# List All Devices

> [!tip] Endpoint
> - **URL:** `/api/devices`
> - **Method:** `GET`
> - **Auth:** Optional (JWT for admin)

Get a list of all devices in the system.

## Query Parameters

| Parameter | Type | Description |
|-----------|------|-------------|
| `limit` | int | Number of results (default: 20) |
| `offset` | int | Pagination offset |

## Response

```json
{
  "total_count": 100,
  "list": [
    {
      "id": 1,
      "name": "ESP32 Device 1",
      "device_type": 2,
      "current_state": 1,
      "created_at": "2024-01-01T00:00:00Z",
      "updated_at": "2024-01-01T00:00:00Z"
    }
  ],
  "devices": [...]
}
```

| Status | Description |
|:------:|-------------|
| 200 | Success |
| 500 | Server error |

> [!note] Pagination
> Use `limit` and `offset` query parameters for pagination.