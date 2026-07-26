---
title: Common Reading Parameters
tags:
  - api
  - readings
  - parameters
---

# Common Request/Response Types

## ReadingListItem

Represents a single voltage/current reading.

```json
{
  "id": 1,
  "device_id": 1,
  "voltage": 12.5,
  "current": 2.3,
  "created_at": "2024-04-24T00:00:00Z"
}
```

| Field | Type | Description |
|-------|------|-------------|
| `id` | uint | Unique reading ID |
| `device_id` | uint | Device that recorded the reading |
| `voltage` | float64 | Voltage in volts |
| `current` | float64 | Current in amperes |
| `created_at` | datetime | Timestamp (UTC, RFC3339) |

## Date Range Parameters

Two date formats are supported depending on the endpoint.

### RFC3339 (Recommended)

Full ISO 8601 format with timezone.

```
start_time=2024-04-24T00:00:00Z
end_time=2024-04-24T23:59:59Z
```

### Simple Date (YYYY-MM-DD)

Short format without time.

```
start_date=2024-04-24
end_date=2024-04-24
```

| Parameter | Endpoint | Format | Required | Default |
|-----------|----------|--------|----------|---------|
| `start_time` / `start_date` | All | RFC3339 / YYYY-MM-DD | No | Beginning of today (UTC) |
| `end_time` / `end_date` | All | RFC3339 / YYYY-MM-DD | No | End of today (UTC) |

## Interval Parameters

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `interval` | duration | `1h` | Sampling interval (e.g., `15m`, `1h`, `2h`) |
| `count` | int | `24` | Number of data points to return (max: 1000) |

## Stats Object

Returned with date range queries.

```json
{
  "max_voltage": 13.5,
  "min_voltage": 12.4,
  "max_current": 3.5,
  "min_current": 1.2,
  "max_voltage_time": "2024-04-24T14:30:00Z",
  "min_voltage_time": "2024-04-24T06:00:00Z"
}
```

## Error Response

All endpoints return errors in this format:

```json
{
  "error": "error message description"
}
```

| Status | Meaning |
|:------:|---------|
| 200 | Success |
| 400 | Invalid parameters |
| 401 | Unauthorized |
| 500 | Server error |