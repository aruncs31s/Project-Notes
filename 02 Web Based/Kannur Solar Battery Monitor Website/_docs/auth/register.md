# Register

> [!tip] Endpoint
> - **URL:** `/api/register`
> - **Method:** `POST`

Register a new user account.

## Request Body

```json
{
  "username": "newuser",
  "email": "newuser@example.com",
  "password": "password123",
  "name": "New User"
}
```

| Field | Type | Required | Description |
|-------|------|:--------:|-------------|
| `username` | string | ✓ | Unique username |
| `email` | string | ✓ | Unique email address |
| `password` | string | ✓ | Password (min 6 characters) |
| `name` | string | | Display name |

## Response

```json
{
  "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
  "refresh_token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
  "user": {
    "id": 2,
    "name": "New User",
    "username": "newuser",
    "email": "newuser@example.com"
  }
}
```

| Status | Description |
|:------:|-------------|
| 200 | Success |
| 400 | Invalid request / duplicate username or email |
| 500 | Server error |

> [!warning] Auto-login
> After registration, the user is automatically logged in and receives tokens.