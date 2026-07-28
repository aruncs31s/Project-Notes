# 18 JWT Authentication

> [!summary] Change Summary
> **Feature**: JWT-based API client authentication using `ACCESS_TOKEN_SECRET`. Admin dashboard supports custom token generation (app name and duration parameters).
> **Status**: Implementation in progress
> Tags:
> Commit:

## Overview

Replaces the simple static `AUTH_TOKEN` authentication with HS256 JWT-based authentication for external API consumers. The admin dashboard provides a UI to generate these JWTs.

## JWT Claims Structure

The generated JWTs will contain:
- `sub`: Recipient application name (e.g. `billing-service`)
- `exp`: Expiration Unix timestamp

## Configuration

Add the following to `.env`:
```env
ACCESS_TOKEN_SECRET=your-secure-jwt-signing-secret
```

New configuration field:
- `AccessTokenSecret` (env `ACCESS_TOKEN_SECRET`): The secret key used for signing and verifying tokens.

## Database Schema (SQLite)

We track generated token metadata to allow the admin to list and revoke active credentials:
```sql
CREATE TABLE IF NOT EXISTS api_tokens (
    id         TEXT PRIMARY KEY,
    app_name   TEXT NOT NULL,
    token      TEXT NOT NULL, -- The full JWT
    expires_at DATETIME NOT NULL,
    created_at DATETIME NOT NULL,
    revoked    INTEGER NOT NULL DEFAULT 0
);
```

## Storage Interface Additions

- `SaveAPIToken(ctx, token)`
- `ListAPITokens(ctx) ([]APIToken, error)`
- `RevokeAPIToken(ctx, id)`
- `IsTokenRevoked(ctx, jti) (bool, error)`

## API Changes

- `POST /admin/api/token/generate` -> Generates JWT, saves metadata, returns it.
- `GET /admin/api/tokens` -> Lists all generated tokens.
- `DELETE /admin/api/tokens/:id` -> Revokes the given token.
- `RequireAuth` middleware -> Extracts JWT from `Authorization` header, verifies HS256 signature against `ACCESS_TOKEN_SECRET`, verifies expiration (`exp`), and verifies it is not revoked in the DB.
