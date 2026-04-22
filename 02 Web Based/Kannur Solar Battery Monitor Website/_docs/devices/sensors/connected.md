---
title: Get Connected Devices
tags:
  - api
  - sensors
  - connected
---

# Get Connected Devices

> [!tip] Endpoint
> - **URL:** `/api/sensors/:id/connected`
> - **Method:** `GET`
> - **Auth:** Optional

Get devices connected to a specific sensor.

## URL Parameters

| Parameter | Type | Description |
|-----------|------|-------------|
| `:id` | uint | Sensor ID |

## Response

```json
{
  "connected_devices": [
    {
      "id": 10,
      "parent_id": 1,
      "name": "ESP32 Main",
      "type": "ESP32 (NODEMCU-32)",
      "ip_address": "192.168.1.100",
      "mac_address": "AA:BB:CC:DD:EE:FF",
      "current_state": "active"
    }
  ]
}
```

| Status | Description |
|:------:|-------------|
| 200 | Success |
| 404 | Sensor not found |
| 500 | Server error |