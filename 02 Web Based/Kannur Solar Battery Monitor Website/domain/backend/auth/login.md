---
title: Login
tags:
  - api
  - auth
  - login
---

# Login

> [!tip] Endpoint
> - **URL:** `/api/login`
> - **Method:** `POST`

Authenticate a user and get access/refresh tokens.

## Request Body

```json
{
  "username": "admin",
  "password": "admin123"
}
```

| Field | Type | Required | Description |
|-------|------|:--------:|-------------|
| `username` | string | ✓ | Username or email |
| `password` | string | ✓ | User password |

## Response

```json
{
  "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
  "refresh_token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
  "user": {
    "id": 1,
    "name": "System Admin",
    "username": "admin",
    "email": "admin@example.com",
    "role": "admin"
  }
}
```

| Status | Description |
|:------:|-------------|
| 200 | Success |
| 400 | Invalid request body |
| 401 | Invalid credentials |
| 500 | Server error |

## Using the Token

Include the access token in the `Authorization` header:

```
Authorization: Bearer <access_token>
```

> [!warning] Token Expiration
> Access token expires in 24 hours. Use [Refresh Token](refresh.md) to get a new one.