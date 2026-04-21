# JWT Structure

## Overview

The LMS uses JWT (JSON Web Tokens) for authentication. Tokens are signed using HMAC-SHA256.

## Token Payload

```go
type UserClaims struct {
    UserID string `json:"user_id"`
    Role   string `json:"role"`
}
```

## Supported Roles

| Role | Description |
|------|-------------|
| `student` | Regular learner |
| `teacher` | Course creator/instructor |
| `admin` | System administrator |

## Token Flow

```
1. User registers/logs in
2. Server validates credentials
3. Server generates JWT with UserClaims
4. Client stores token
5. Client sends token in Authorization header
```

## Usage in Requests

```http
Authorization: Bearer <jwt_token>
```

## Middleware Functions

| Function | Description |
|----------|-------------|
| `AuthMiddleware` | Validates JWT, extracts claims |
| `OptionalAuthMiddleware` | Extracts claims if token present |
| `RoleMiddleware` | Checks if user has required role |

## Implementation

**File**: `internal/middleware/auth.go`

**Secret**: Configured via `JWT_SECRET` environment variable