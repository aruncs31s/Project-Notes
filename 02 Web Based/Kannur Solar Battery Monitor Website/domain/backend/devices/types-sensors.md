---
title: Get Sensor Types
tags:
  - api
  - devices
  - sensors
---

# Get Sensor Types

> [!tip] Endpoint
> - **URL:** `/api/devices/types/sensors`
> - **Method:** `GET`
> - **Auth:** Optional

Get a list of all sensor hardware types.

## Response

```json
{
  "sensor_types": [
    {
      "id": 3,
      "name": "Sensor"
    },
    {
      "id": 5,
      "name": "VoltageMeter"
    },
    {
      "id": 6,
      "name": "CurrentSensor"
    },
    {
      "id": 7,
      "name": "PowerMeter"
    }
  ]
}
```

| Status | Description |
|:------:|-------------|
| 200 | Success |
| 500 | Server error |

> [!note] Use Case
> This endpoint helps filter devices that are sensors for display in sensor-specific UI components.