---
title: Create Reading
tags:
  - api
  - readings
  - create
---

# Create Reading

> [!tip] Endpoint
> - **URL:** `/api/readings`
> - **Method:** `POST`
> - **Auth:** Device Token

Submit a new reading from a device.

## Request Body

```json
{
  "voltage": 12.5,
  "current": 2.5
}
```

| Field | Type | Required | Description |
|-------|------|:--------:|-------------|
| `voltage` | float64 | ✓ | Voltage reading (V) |
| `current` | float64 | ✓ | Current reading (A) |

## Headers

| Header | Description |
|--------|-------------|
| `Authorization` | Device token (Bearer) |

## Response

```json
{
  "reading": {
    "id": 100,
    "device_id": 1,
    "voltage": 12.5,
    "current": 2.5,
    "created_at": "2024-01-01T12:00:00Z"
  }
}
```

| Status | Description |
|:------:|-------------|
| 201 | Reading created |
| 400 | Invalid request |
| 401 | Unauthorized (invalid device token) |
| 500 | Server error |

> [!note] Device Auth
> This endpoint uses device token authentication, not user JWT. The device ID is extracted from the token.