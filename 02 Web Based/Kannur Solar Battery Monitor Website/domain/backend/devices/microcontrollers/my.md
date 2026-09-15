---
title: My Microcontrollers
tags:
  - api
  - microcontrollers
  - my
---

# My Microcontrollers

> [!tip] Endpoint
> - **URL:** `/api/devices/microcontrollers/my`
> - **Method:** `GET`
> - **Auth:** Required (JWT)

Get microcontrollers owned by the authenticated user.

## Query Parameters

| Parameter | Type | Description |
|-----------|------|-------------|
| `limit` | int | Number of results (default: 20) |
| `offset` | int | Pagination offset |

## Response

```json
{
  "microcontrollers": [
    {
      "id": 1,
      "name": "My ESP32 Controller",
      "type": "ESP32 (NODEMCU-32)",
      "ip_address": "192.168.1.100",
      "current_state": "active"
    }
  ]
}
```

| Status | Description |
|:------:|-------------|
| 200 | Success |
| 401 | Unauthorized |
| 500 | Server error |