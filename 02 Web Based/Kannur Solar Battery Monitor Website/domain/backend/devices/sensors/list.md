---
title: List Sensors
tags:
  - api
  - sensors
  - list
---

# List Sensors

> [!tip] Endpoint
> - **URL:** `/api/sensors`
> - **Method:** `GET`
> - **Auth:** Optional

Get a list of all sensor devices.

## Query Parameters

| Parameter | Type | Description |
|-----------|------|-------------|
| `limit` | int | Number of results (default: 20) |
| `offset` | int | Pagination offset |

## Response

```json
{
  "total_count": 50,
  "sensors": [
    {
      "id": 1,
      "name": "Temperature Sensor 1",
      "device_type": 3,
      "current_state": 1,
      "type": "Temperature Sensor",
      "hardware_type": 3
    }
  ]
}
```

| Status | Description |
|:------:|-------------|
| 200 | Success |
| 500 | Server error |