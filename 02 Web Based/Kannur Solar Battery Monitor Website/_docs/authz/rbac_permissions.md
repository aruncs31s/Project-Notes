# Role-Based Access Control (RBAC) Documentation

This document explains how permissions are handled in the SKVMS backend using Casbin.

## 1. Core Permissions (Actions)

We use three primary actions to define what a user can do with a device:

| Action | Description | APIs Affected |
| :--- | :--- | :--- |
| **`view`** | Read device details, status, and historical data. | `GET /devices/:id`, `GET /readings`, etc. |
| **`control`** | Send real-time commands to the hardware. | `POST /devices/:id/control` |
| **`manage`** | Administrative full control over the record. | `PUT`, `DELETE`, and Ownership Transfer. |

## 2. The Casbin Middleware

The middleware `middleware.CasbinDeviceAuth(action)` works by checking for a device ID in the URL.

### How it works:
1. **Extraction**: It looks for the `:id` parameter in the route (e.g., in `/api/devices/:id`).
2. **Identity**: It gets the `user_id` from the JWT token.
3. **Enforcement**: It checks the database for a policy matching `(user:ID, device:ID, action)`.
4. **Fallback**: If the device is marked as `is_public` and the action is `view`, it allows access even without a specific user policy.
5. **Admin Override**: Users with the `admin` role bypass all checks.

## 3. The "List Devices" Case

You applied the middleware to the root devices route:
```go
device.GET("", middleware.JWTAuth(r.jwtSecret), middleware.CasbinDeviceAuth("view"), r.deviceHandler.ListDevices)
```

### The Effect:
**Currently, this has NO restricting effect.** 

Because the route `GET /api/devices` does not contain an `:id` parameter, the middleware sees an empty device ID and allows the request to pass through via `c.Next()`. 

To restrict which devices appear in a list, you should handle filtering in the **Service Layer** by querying only the devices that the `user_id` has permission to see, rather than using the middleware.

## 4. How to Grant Permissions

Permissions are automatically granted when:
- A user creates a device (they become the "Owner").
- Ownership is transferred to a new user.

### Manual Granting (Backend)
To manually give a user access to a specific device, you use the internal package:
```go
casbinPkg.GrantDevicePermission("user:5", "device:10", "view")
```

## 5. Database Storage
All permissions are stored in the `casbin_rule` table in your database. This allows you to manage access dynamically without changing code.
