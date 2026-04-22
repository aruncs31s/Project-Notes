---
title: Create Device
tags:
  - api
  - devices
  - create
---

# Create Device

> [!tip] Endpoint
> - **URL:** `/api/devices`
> - **Method:** `POST`
> - **Auth:** Required (JWT)

Create a new device in the system.

## Request Body

```json
{
  "name": "New ESP32 Device",
  "device_type": 2,
  "version_id": 1,
  "is_public": false
}
```

| Field | Type | Required | Description |
|-------|------|:--------:|-------------|
| `name` | string | ✓ | Device name |
| `device_type` | uint | ✓ | Device type ID |
| `version_id` | uint | | Version ID |
| `is_public` | bool | | Make device publicly accessible |

## Response

```json
{
  "device": {
    "id": 10,
    "name": "New ESP32 Device",
    "device_type": 2,
    "current_state": 1,
    "created_at": "2024-01-01T00:00:00Z"
  }
}
```

| Status | Description |
|:------:|-------------|
| 201 | Device created successfully |
| 400 | Invalid request body |
| 401 | Unauthorized |
| 500 | Server error |

> [!note] Audit Log
> This action is logged in the audit logs.