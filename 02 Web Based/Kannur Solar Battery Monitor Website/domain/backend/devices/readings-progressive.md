---
title: Get Progressive Readings
tags:
  - api
  - devices
  - readings
---

# Get Progressive Readings

> [!tip] Endpoint
> - **URL:** `/api/devices/:id/readings/progressive`
> - **Method:** `GET`
> - **Auth:** Optional

Get device readings with running averages (progressive/cumulative).

## URL Parameters

| Parameter | Type | Description |
|-----------|------|-------------|
| `:id` | uint | Device ID |

## Response

```json
{
  "readings": [
    {
      "created_at": "2024-01-01T12:00:00Z",
      "voltage": 12.5,
      "current": 2.5,
      "power": 31.25,
      "avg_voltage": 12.5,
      "avg_current": 2.5
    },
    {
      "created_at": "2024-01-01T12:10:00Z",
      "voltage": 12.8,
      "current": 2.7,
      "power": 34.56,
      "avg_voltage": 12.65,
      "avg_current": 2.6
    }
  ]
}
```

| Field | Type | Description |
|-------|------|-------------|
| `voltage` | float64 | Current voltage |
| `current` | float64 | Current current |
| `power` | float64 | Calculated power (V × I) |
| `avg_voltage` | float64 | Running average voltage (last 50 readings) |
| `avg_current` | float64 | Running average current (last 50 readings) |

| Status | Description |
|:------:|-------------|
| 200 | Success |
| 400 | Invalid device ID |
| 500 | Server error |

> [!note] Moving Average
> The averages are calculated using a window of 50 preceding readings.