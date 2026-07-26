---
title: Full Update Device
tags:
  - api
  - devices
  - update
---

# Full Update Device

> [!tip] Endpoint
> - **URL:** `/api/devices/:id/full`
> - **Method:** `PUT`
> - **Auth:** Required (JWT)

Perform a full update on a device (replaces all fields).

## URL Parameters

| Parameter | Type | Description |
|-----------|------|-------------|
| `:id` | uint | Device ID |

## Request Body

```json
{
  "name": "Full Updated Device",
  "device_type": 2,
  "current_state": 1,
  "version_id": 1,
  "is_public": false,
  "updated_by": 1
}
```

| Field | Type | Required | Description |
|-------|------|:--------:|-------------|
| `name` | string | ✓ | Device name |
| `device_type` | uint | ✓ | Device type ID |
| `current_state` | uint | ✓ | Device state |
| `version_id` | uint | | Version ID |
| `is_public` | bool | | Public status |
| `updated_by` | uint | | User ID updating |

## Response

```json
{
  "message": "device updated successfully"
}
```

| Status | Description |
|:------:|-------------|
| 200 | Device updated successfully |
| 400 | Invalid request |
| 401 | Unauthorized |
| 404 | Device not found |
| 500 | Server error |

> [!warning] Full Replacement
> This replaces all device fields. Missing fields may be reset to defaults.