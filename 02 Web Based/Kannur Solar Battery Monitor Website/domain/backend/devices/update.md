---
title: Update Device
tags:
  - api
  - devices
  - update
---

# Update Device

> [!tip] Endpoint
> - **URL:** `/api/devices/:id`
> - **Method:** `PUT`
> - **Auth:** Required (JWT)

Update an existing device's information.

## URL Parameters

| Parameter | Type | Description |
|-----------|------|-------------|
| `:id` | uint | Device ID |

## Request Body

```json
{
  "name": "Updated Device Name",
  "current_state": 2,
  "is_public": true
}
```

| Field | Type | Required | Description |
|-------|------|:--------:|-------------|
| `name` | string | | Updated device name |
| `current_state` | uint | | Updated state (1=Active, 2=Inactive, etc.) |
| `is_public` | bool | | Update public status |

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

> [!note] Partial Update
> Only fields provided in the request body will be updated.