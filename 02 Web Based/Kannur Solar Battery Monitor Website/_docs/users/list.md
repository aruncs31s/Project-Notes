# List Users

> [!tip] Endpoint
> - **URL:** `/api/users`
> - **Method:** `GET`
> - **Auth:** Required (JWT)

Get a list of all users in the system.

## Query Parameters

| Parameter | Type | Description |
|-----------|------|-------------|
| - | - | No query parameters required |

## Response

```json
{
  "users": [
    {
      "id": 1,
      "name": "System Admin",
      "username": "admin",
      "email": "admin@example.com",
      "role": "admin",
      "created_at": "2024-01-01T00:00:00Z"
    }
  ]
}
```

| Status | Description |
|:------:|-------------|
| 200 | Success |
| 401 | Unauthorized |
| 500 | Server error |

> [!note] Required Role
> Typically requires admin role or higher privileges.