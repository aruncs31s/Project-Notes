---
title: List Device Types
tags:
  - api
  - devices
  - types
---

# List Device Types

> [!tip] Endpoint
> - **URL:** `/api/devices/types`
> - **Method:** `GET`
> - **Auth:** Optional

Get a list of all device types.

## Response

```json
{
  "device_types": [
    {
      "id": 1,
      "name": "ESP8266 (NODEMCU)",
      "hardware_type": 1
    },
    {
      "id": 2,
      "name": "ESP32 (NODEMCU-32)",
      "hardware_type": 1
    }
  ]
}
```

| Status | Description |
|:------:|-------------|
| 200 | Success |
| 500 | Server error |