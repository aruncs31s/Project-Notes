---
title: Advanced Readings
tags:
  - api
  - readings
  - advanced
---

# Advanced Readings

> [!tip] Endpoint
> - **URL:** `/api/readings`
> - **Method:** `GET`
> - **Auth:** Required (JWT)

Get readings with advanced filtering options.

## Query Parameters

| Parameter | Type | Description |
|-----------|------|-------------|
| `device_id` | uint | Filter by device ID |
| `location_id` | uint | Filter by location ID |
| `start_time` | datetime | Start time (ISO 8601) |
| `end_time` | datetime | End time (ISO 8601) |
| `limit` | int | Number of results (default: 100, max: 5000) |
| `offset` | int | Pagination offset |

## Example Request

```
GET /api/readings?device_id=1&start_time=2024-01-01T00:00:00&end_time=2024-01-07T00:00:00&limit=100
```

## Response

```json
{
  "readings": [
    {
      "device_id": 1,
      "device_name": "ESP32 Device 1",
      "voltage": 12.5,
      "current": 2.5,
      "power": 31.25,
      "timestamp": "2024-01-01T12:00:00Z"
    }
  ],
  "total": 1000
}
```

| Status | Description |
|:------:|-------------|
| 200 | Success |
| 401 | Unauthorized |
| 500 | Server error |