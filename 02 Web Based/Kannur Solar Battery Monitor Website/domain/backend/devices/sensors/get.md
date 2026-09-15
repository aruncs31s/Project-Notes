---
title: Get Sensor
tags:
  - api
  - sensors
  - get
---

# Get Sensor

> [!tip] Endpoint
> - **URL:** `/api/sensors/:id`
> - **Method:** `GET`
> - **Auth:** Optional

Get a specific sensor by ID.

## URL Parameters

| Parameter | Type | Description |
|-----------|------|-------------|
| `:id` | uint | Sensor ID |

## Response

```json
{
  "sensor": {
    "id": 1,
    "name": "Temperature Sensor 1",
    "device_type": 3,
    "current_state": 1,
    "type": "Temperature Sensor",
    "hardware_type": 3
  }
}
```

| Status | Description |
|:------:|-------------|
| 200 | Success |
| 404 | Sensor not found |
| 500 | Server error |