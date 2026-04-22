---
title: Search Devices
tags:
  - api
  - devices
  - search
---

# Search Devices

> [!tip] Endpoint
> - **URL:** `/api/devices/search`
> - **Method:** `GET`
> - **Auth:** Required (JWT)

Search for devices by name or other criteria.

## Query Parameters

| Parameter | Type | Description |
|-----------|------|-------------|
| `q` | string | Search query (device name) |
| `limit` | int | Number of results (default: 20) |
| `offset` | int | Pagination offset |

## Example Request

```
GET /api/devices/search?q=esp32&limit=10
```

## Response

```json
{
  "devices": [
    {
      "id": 1,
      "name": "ESP32 Device 1",
      "device_type": 2,
      "current_state": 1
    }
  ]
}
```

| Status | Description |
|:------:|-------------|
| 200 | Success |
| 401 | Unauthorized |
| 500 | Server error |