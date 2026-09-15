---
title: Recent Devices
tags:
  - api
  - devices
  - recent
---

# Recent Devices

> [!tip] Endpoint
> - **URL:** `/api/devices/recent`
> - **Method:** `GET`
> - **Auth:** Optional

Get a list of recently active devices.

## Query Parameters

| Parameter | Type | Description |
|-----------|------|-------------|
| `limit` | int | Number of results (default: 20) |
| `offset` | int | Pagination offset |

## Response

```json
{
  "total_count": 50,
  "list": [
    {
      "id": 1,
      "name": "ESP32 Device 1",
      "device_type": 2,
      "current_state": 1,
      "updated_at": "2024-01-01T12:00:00Z"
    }
  ]
}
```

| Status | Description |
|:------:|-------------|
| 200 | Success |
| 500 | Server error |

> [!note] Sorted by Updated At
> Results are sorted by most recently updated devices first.