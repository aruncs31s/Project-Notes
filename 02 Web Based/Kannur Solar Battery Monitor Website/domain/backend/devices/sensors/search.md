---
title: Search Sensors
tags:
  - api
  - sensors
  - search
---

# Search Sensors

> [!tip] Endpoint
> - **URL:** `/api/sensors/search`
> - **Method:** `GET`
> - **Auth:** Optional

Search for sensors by name.

## Query Parameters

| Parameter | Type | Description |
|-----------|------|-------------|
| `q` | string | Search query |
| `limit` | int | Number of results (default: 20) |
| `offset` | int | Pagination offset |

## Example Request

```
GET /api/sensors/search?q=temperature
```

## Response

```json
{
  "sensors": [
    {
      "id": 1,
      "name": "Temperature Sensor 1",
      "device_type": 3,
      "current_state": 1
    }
  ]
}
```

| Status | Description |
|:------:|-------------|
| 200 | Success |
| 500 | Server error |