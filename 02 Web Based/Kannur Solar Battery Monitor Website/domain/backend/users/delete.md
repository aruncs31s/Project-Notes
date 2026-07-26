---
title: Delete User
tags:
  - api
  - users
  - delete
---

# Delete User

> [!tip] Endpoint
> - **URL:** `/api/users/:id`
> - **Method:** `DELETE`
> - **Auth:** Required (JWT)

Delete a user from the system.

## URL Parameters

| Parameter | Type | Description |
|-----------|------|-------------|
| `:id` | uint | User ID |

## Response

```json
{
  "message": "user deleted successfully"
}
```

| Status | Description |
|:------:|-------------|
| 200 | User deleted successfully |
| 400 | Invalid user ID |
| 401 | Unauthorized |
| 404 | User not found |
| 500 | Server error |

> [!warning] Irreversible Action
> This action permanently deletes the user and cannot be undone.

> [!note] Audit Log
> This action is logged in the audit logs.