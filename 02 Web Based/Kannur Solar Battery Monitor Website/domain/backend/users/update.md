---
title: Update User
tags:
  - api
  - users
  - update
---

# Update User

> [!tip] Endpoint
> - **URL:** `/api/users/:id`
> - **Method:** `PUT`
> - **Auth:** Required (JWT)

Update an existing user's information.

## URL Parameters

| Parameter | Type | Description |
|-----------|------|-------------|
| `:id` | uint | User ID |

## Request Body

```json
{
  "name": "Updated Name",
  "email": "updated@example.com",
  "role": "admin"
}
```

| Field | Type | Required | Description |
|-------|------|:--------:|-------------|
| `name` | string | | Updated display name |
| `email` | string | | Updated email address |
| `role` | string | | Updated role |

## Response

```json
{
  "message": "user updated successfully"
}
```

| Status | Description |
|:------:|-------------|
| 200 | User updated successfully |
| 400 | Invalid request body |
| 401 | Unauthorized |
| 404 | User not found |
| 500 | Server error |

> [!note] Audit Log
> This action is logged in the audit logs.