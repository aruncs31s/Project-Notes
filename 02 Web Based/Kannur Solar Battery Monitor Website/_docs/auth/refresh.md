---
title: Refresh Token
tags:
  - api
  - auth
  - token
---

# Refresh Token

> [!tip] Endpoint
> - **URL:** `/api/refresh`
> - **Method:** `POST`

Refresh an expired access token using a refresh token.

## Request Body

```json
{
  "refresh_token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..."
}
```

| Field | Type | Required | Description |
|-------|------|:--------:|-------------|
| `refresh_token` | string | ✓ | The refresh token received from login |

## Response

```json
{
  "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
  "refresh_token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..."
}
```

| Status | Description |
|:------:|-------------|
| 200 | Success |
| 400 | Invalid request body |
| 401 | Invalid or expired refresh token |

## Token Expiration

| Token | Expiration |
|-------|------------|
| Access Token | 24 hours |
| Refresh Token | 7 days |

> [!note] When to Use
> Use this endpoint when the access token has expired. You do not need to re-authenticate with username/password.