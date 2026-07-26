---
title: Delete Device
tags:
  - api
  - devices
  - delete
---

# Delete Device

> [!tip] Endpoint
> - **URL:** `/api/devices/:id`
> - **Method:** `DELETE`
> - **Auth:** Required (JWT)

Delete a device from the system.

## URL Parameters

| Parameter | Type | Description |
|-----------|------|-------------|
| `:id` | uint | Device ID |

## Response

```json
{
  "message": "device deleted successfully"
}
```

| Status | Description |
|:------:|-------------|
| 200 | Device deleted successfully |
| 400 | Invalid device ID |
| 401 | Unauthorized |
| 404 | Device not found |
| 500 | Server error |

> [!warning] Irreversible Action
> This action permanently deletes the device and cannot be undone.

> [!note] Audit Log
> This action is logged in the audit logs.

> [!note] Admin Protection
> Devices created by admin cannot be deleted.