---
title: Create Device Type
tags:
  - api
  - devices
  - types
---

# Create Device Type

> [!tip] Endpoint
> - **URL:** `/api/devices/types`
> - **Method:** `POST`
> - **Auth:** Required (JWT)

Create a new device type.

## Request Body

```json
{
  "name": "New Sensor Device",
  "hardware_type": 3
}
```

| Field | Type | Required | Description |
|-------|------|:--------:|-------------|
| `name` | string | ✓ | Device type name |
| `hardware_type` | uint | ✓ | Hardware type ID |

### Hardware Types

| ID | Type |
|:--:|------|
| 1 | MicroController |
| 2 | SingleBoardComputer |
| 3 | Sensor |
| 4 | Solar |
| 5 | VoltageMeter |
| 6 | CurrentSensor |
| 7 | PowerMeter |
| 8 | Actuator |

## Response

```json
{
  "device_type": {
    "id": 10,
    "name": "New Sensor Device",
    "hardware_type": 3
  }
}
```

| Status | Description |
|:------:|-------------|
| 201 | Device type created |
| 400 | Invalid request |
| 401 | Unauthorized |
| 500 | Server error |