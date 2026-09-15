---
title: Get Device Readings
tags:
  - api
  - devices
  - readings
---

# Get Device Readings

> [!tip] Endpoint
> - **URL:** `/api/devices/:id/readings`
> - **Method:** `GET`
> - **Auth:** Optional

Get a list of readings for a specific device.

## URL Parameters

| Parameter | Type | Description |
|-----------|------|-------------|
| `:id` | uint | Device ID |

## Query Parameters

| Parameter | Type | Description |
|-----------|------|-------------|
| `limit` | int | Number of results (default: 20) |
| `offset` | int | Pagination offset |

## Response

```json
{
  "readings": [
    {
      "id": 1,
      "device_id": 1,
      "voltage": 12.5,
      "current": 2.5,
      "created_at": "2024-01-01T12:00:00Z"
    }
  ]
}
```

| Status | Description |
|:------:|-------------|
| 200 | Success |
| 400 | Invalid device ID |
| 500 | Server error |