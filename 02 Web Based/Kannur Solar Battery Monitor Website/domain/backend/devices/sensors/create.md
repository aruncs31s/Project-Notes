---
title: Create Sensor
tags:
  - api
  - sensors
  - create
---

# Create Sensor

> [!tip] Endpoint
> - **URL:** `/api/sensors`
> - **Method:** `POST`
> - **Auth:** Optional (or JWT if required)

Create a new sensor device.

## Request Body

```json
{
  "name": "New Temperature Sensor",
  "device_type": 3,
  "ip_address": "192.168.1.50",
  "mac_address": "AA:BB:CC:DD:EE:11"
}
```

| Field | Type | Required | Description |
|-------|------|:--------:|-------------|
| `name` | string | ✓ | Sensor name |
| `device_type` | uint | ✓ | Device type ID |
| `ip_address` | string | | IP address |
| `mac_address` | string | | MAC address |

## Response

```json
{
  "sensor": {
    "id": 10,
    "name": "New Temperature Sensor",
    "device_type": 3,
    "current_state": 1,
    "created_at": "2024-01-01T00:00:00Z"
  }
}
```

| Status | Description |
|:------:|-------------|
| 201 | Sensor created |
| 400 | Invalid request |
| 500 | Server error |