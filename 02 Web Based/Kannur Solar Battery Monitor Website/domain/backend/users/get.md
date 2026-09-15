---
title: Get User
tags:
  - api
  - users
  - get
---

# Get User

> [!tip] Endpoint
> - **URL:** `/api/users/:id`
> - **Method:** `GET`
> - **Auth:** Required (JWT)

Get a specific user by ID.

## URL Parameters

| Parameter | Type | Description |
|-----------|------|-------------|
| `:id` | uint | User ID |

## Response

```json
{
  "user": {
    "id": 1,
    "name": "System Admin",
    "username": "admin",
    "email": "admin@example.com",
    "role": "admin",
    "created_at": "2024-01-01T00:00:00Z"
  }
}
```

| Status | Description |
|:------:|-------------|
| 200 | Success |
| 400 | Invalid user ID |
| 401 | Unauthorized |
| 404 | User not found |
| 500 | Server error |