---
title: Get Device
tags:
  - api
  - devices
  - get
---

# Get Device

> [!tip] Endpoint
> - **URL:** `/api/devices/:id`
> - **Method:** `GET`
> - **Auth:** Optional

Get a specific device by ID.

## URL Parameters

| Parameter | Type | Description |
|-----------|------|-------------|
| `:id` | uint | Device ID |

## Response

```json
{
  "device": {
    "id": 1,
    "name": "ESP32 Device 1",
    "device_type": 2,
    "current_state": 1,
    "version_id": 1,
    "created_by": 1,
    "updated_by": 1,
    "created_at": "2024-01-01T00:00:00Z",
    "updated_at": "2024-01-01T00:00:00Z"
  }
}
```

| Status | Description |
|:------:|-------------|
| 200 | Success |
| 400 | Invalid device ID |
| 404 | Device not found |
| 500 | Server error |