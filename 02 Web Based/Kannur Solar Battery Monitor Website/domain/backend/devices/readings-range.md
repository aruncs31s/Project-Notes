---
title: Get Readings by Date Range
tags:
  - api
  - devices
  - readings
---

# Get Readings by Date Range

> [!tip] Endpoint
> - **URL:** `/api/devices/:id/readings/range`
> - **Method:** `GET`
> - **Auth:** Optional

Get device readings within a specific date range.

## URL Parameters

| Parameter | Type | Description |
|-----------|------|-------------|
| `:id` | uint | Device ID |

## Query Parameters

| Parameter | Type | Description |
|-----------|------|-------------|
| `start_date` | date | Start date (YYYY-MM-DD) |
| `end_date` | date | End date (YYYY-MM-DD) |

## Example Request

```
GET /api/devices/1/readings/range?start_date=2024-01-01&end_date=2024-01-07
```

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
  ],
  "stats": {
    "max_voltage": 14.5,
    "min_voltage": 10.5,
    "max_current": 5.0,
    "min_current": 0.5
  }
}
```

| Status | Description |
|:------:|-------------|
| 200 | Success |
| 400 | Invalid parameters |
| 500 | Server error |

> [!note] Stats Included
> Response includes min/max voltage and current for the date range.