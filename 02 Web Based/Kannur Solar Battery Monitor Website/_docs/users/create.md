# Create User

> [!tip] Endpoint
> - **URL:** `/api/users`
> - **Method:** `POST`
> - **Auth:** Required (JWT)

Create a new user in the system.

## Request Body

```json
{
  "username": "newuser",
  "email": "newuser@example.com",
  "password": "password123",
  "name": "New User",
  "role": "user"
}
```

| Field | Type | Required | Description |
|-------|------|:--------:|-------------|
| `username` | string | ✓ | Unique username |
| `email` | string | ✓ | Unique email address |
| `password` | string | ✓ | Password (min 6 characters) |
| `name` | string | | Display name |
| `role` | string | | User role (default: "user") |

## Response

```json
{
  "message": "user created successfully"
}
```

| Status | Description |
|:------:|-------------|
| 201 | User created successfully |
| 400 | Invalid request body |
| 401 | Unauthorized |
| 500 | Server error |

> [!warning] Duplicate Check
> Username and email must be unique. Returns 400 if duplicates exist.