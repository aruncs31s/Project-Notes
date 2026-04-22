# Get Profile

> [!tip] Endpoint
> - **URL:** `/api/profile`
> - **Method:** `GET`
> - **Auth:** Required (JWT)

Get the profile of the currently authenticated user.

## Response

```json
{
  "profile": {
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
| 401 | Unauthorized (not authenticated) |
| 500 | Server error |

> [!note] No Parameter Needed
> This endpoint uses the authenticated user's ID from the JWT token - no URL parameters needed.