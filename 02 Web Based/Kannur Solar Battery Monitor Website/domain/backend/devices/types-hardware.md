---
title: Get Hardware Types
tags:
  - api
  - devices
  - hardware
---

# Get Hardware Types

> [!tip] Endpoint
> - **URL:** `/api/devices/types/hardware`
> - **Method:** `GET`
> - **Auth:** Required (JWT)

Get a list of all available hardware types.

## Response

```json
{
  "hardware_types": [
    {
      "id": 1,
      "name": "MicroController"
    },
    {
      "id": 2,
      "name": "SingleBoardComputer"
    },
    {
      "id": 3,
      "name": "Sensor"
    },
    {
      "id": 4,
      "name": "Solar"
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
    },
    {
      "id": 8,
      "name": "Actuator"
    }
  ]
}
```

| Status | Description |
|:------:|-------------|
| 200 | Success |
| 401 | Unauthorized |
| 500 | Server error |