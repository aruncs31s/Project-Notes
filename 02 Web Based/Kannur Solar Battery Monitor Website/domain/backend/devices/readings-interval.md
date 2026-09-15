---
title: Get Readings with Interval
tags:
  - api
  - devices
  - readings
---

# Get Readings with Interval

> [!tip] Endpoint
> - **URL:** `/api/devices/:id/readings/interval`
> - **Method:** `GET`
> - **Auth:** Optional

Get device readings at regular time intervals.

## URL Parameters

| Parameter | Type | Description |
|-----------|------|-------------|
| `:id` | uint | Device ID |

## Query Parameters

| Parameter | Type | Description |
|-----------|------|-------------|
| `start_time` | datetime | Start time (ISO 8601) |
| `end_time` | datetime | End time (ISO 8601) |
| `interval` | duration | Interval (e.g., "1h", "30m", "15s") |
| `count` | int | Maximum number of points (default: 50, max: 1000) |

## Example Request

```
GET /api/devices/1/readings/interval?start_time=2024-01-01T00:00:00&end_time=2024-01-07T00:00:00&interval=1h&count=100
```

## Response

```json
{
  "readings": [
    {
      "device_id": 1,
      "voltage": 12.5,
      "current": 2.5,
      "created_at": "2024-01-01T00:00:00Z"
    },
    {
      "device_id": 1,
      "voltage": 12.8,
      "current": 2.7,
      "created_at": "2024-01-01T01:00:00Z"
    }
  ]
}
```

| Status | Description |
|:------:|-------------|
| 200 | Success |
| 400 | Invalid parameters |
| 500 | Server error |

> [!note] Use Case
> Useful for creating time-series charts with evenly spaced data points.