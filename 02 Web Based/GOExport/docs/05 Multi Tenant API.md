---
created: 2026-07-28
tags:
  - goexport
  - docs
  - api
  - multitenancy
status: draft
type: api-doc
---

# 05 Multi Tenant API

## Configuration

Set `MULTI_TENANT_ENABLED=true` in your `.env` or environment variables to enable multi-tenant isolation mode.

## Request Headers

When multi-tenancy is enabled, client requests (or API Gateway calls) must supply:

| Header | Description | Required | Example |
|--------|-------------|----------|---------|
| `X-Tenant-ID` | Institute or tenant identifier | Yes (when multi-tenant mode active) | `inst-101` |
| `X-User-ID` | End-user identifier | Yes (when multi-tenant mode active) | `user-42` |

---

## Endpoints

### `GET /exports/my` — List My Exports

Returns paginated exports created by the requesting user within their tenant.

**Request**
```http
GET /exports/my?section=academics&limit=20&offset=0 HTTP/1.1
X-Tenant-ID: inst-101
X-User-ID: user-42
```

**Response — 200 OK**
```json
{
  "exports": [
    {
      "id": "550e8400-...",
      "url": "https://example.com/transcript",
      "section": "academics",
      "tenant_id": "inst-101",
      "user_id": "user-42",
      "state": "completed",
      "object_key": "exports/inst-101/academics/550e8400-....pdf",
      "created_at": "2026-07-28T00:00:00Z",
      "completed_at": "2026-07-28T00:00:45Z"
    }
  ],
  "count": 1,
  "total": 1,
  "limit": 20,
  "offset": 0
}
```

---

### Access Control Rules (`GET /exports/:id` and `GET /exports/:id/pdf`)

When `MULTI_TENANT_ENABLED=true`:
- An export created by `TenantID=inst-101` and `UserID=user-42` can only be viewed or downloaded if the caller passes `X-Tenant-ID: inst-101` and `X-User-ID: user-42`.
- Attempting to view or download another user's export returns `403 Forbidden`.

```http
HTTP/1.1 403 Forbidden
Content-Type: application/json

{
  "error": "access denied: you do not have permission to access this export"
}
```

---

> [!summary] Change Summary
> Documented Multi-Tenant headers, `GET /exports/my` endpoint, and security isolation behavior.

**Tags:** 
**Commit:** 
**PR / Branch:** 
