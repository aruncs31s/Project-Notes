---
title: List Microcontrollers
tags:
  - api
  - microcontrollers
  - list
---

# List Microcontrollers

> [!tip] Endpoint
> - **URL:** `/api/devices/microcontrollers`
> - **Method:** `GET`
> - **Auth:** Optional

Get a list of all microcontroller devices.

## Query Parameters

| Parameter | Type | Description |
|-----------|------|-------------|
| `limit` | int | Number of results (default: 20) |
| `offset` | int | Pagination offset |

## Response

```json
{
  "total_count": 25,
  "microcontrollers": [
    {
      "id": 1,
      "name": "ESP32 Main Controller",
      "type": "ESP32 (NODEMCU-32)",
      "ip_address": "192.168.1.100",
      "mac_address": "AA:BB:CC:DD:EE:FF",
      "firmware_version": "1.0.0",
      "current_state": "active"
    }
  ]
}
```

| Status | Description |
|:------:|-------------|
| 200 | Success |
| 500 | Server error |