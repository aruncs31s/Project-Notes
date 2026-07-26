---
title: Control Device
tags:
  - api
  - devices
  - control
---

# Control Device

> [!tip] Endpoint
> - **URL:** `/api/devices/:id/control`
> - **Method:** `POST`
> - **Auth:** Required (JWT)

Send a control command to a device (turn on/off, configure, etc.).

## URL Parameters

| Parameter | Type | Description |
|-----------|------|-------------|
| `:id` | uint | Device ID |

## Request Body

```json
{
  "action": "turn_on"
}
```

| Field | Type | Required | Description |
|-------|------|:--------:|-------------|
| `action` | string | ✓ | Action to perform |

### Available Actions

| Action | Description |
|--------|-------------|
| `turn_on` | Turn the device on |
| `turn_off` | Turn the device off |
| `configure` | Update device configuration |

## Response

```json
{
  "message": "device controlled successfully",
  "state": 1
}
```

| Status | Description |
|:------:|-------------|
| 200 | Success |
| 400 | Invalid request |
| 401 | Unauthorized |
| 404 | Device not found |
| 500 | Server error |

> [!note] State Transitions
> The device state is updated based on the action. Check `DeviceStateActionResult` for valid transitions.