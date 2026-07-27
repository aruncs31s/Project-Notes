---
created: 2026-07-28
tags:
  - goexport
  - docs
  - api
  - exports
status: draft
type: api-doc
---

# 02 Exports API

## `POST /exports` — Create Export

**Request**
```http
POST /exports HTTP/1.1
Content-Type: application/json

{
  "url": "https://example.com/report",
  "section": "academics",
  "callback_url": "https://myapp.com/webhooks/export"
}
```

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| `url` | string | ✅ | Must be absolute `http://` or `https://` URL |
| `section` | string | ❌ | Lowercase letters, numbers, hyphens. Defaults to `general` |
| `callback_url` | string | ❌ | Absolute `http://` or `https://` URL. POSTed to on completion |

**Response — 202 Accepted**
```json
{
  "id": "550e8400-e29b-41d4-a716-446655440000",
  "section": "academics",
  "state": "queued"
}
```

**Error Responses**

| Status | Body | Cause |
|--------|------|-------|
| 400 | `{"error":"url must be an absolute HTTP(S) URL"}` | Invalid URL |
| 400 | `{"error":"section must use lowercase letters, numbers, and hyphens"}` | Invalid section |
| 400 | `{"error":"callback_url must be an absolute HTTP(S) URL"}` | Invalid callback URL |
| 503 | `{"error":"could not queue export"}` | Broker unavailable |

---

## `GET /exports` — List Exports

**Request**
```http
GET /exports?section=academics&limit=20&offset=0 HTTP/1.1
```

| Query Param | Type | Default | Max | Notes |
|-------------|------|---------|-----|-------|
| `section` | string | — | — | Filter by section |
| `limit` | int | 50 | 200 | Number of results |
| `offset` | int | 0 | — | Pagination offset |

**Response — 200 OK**
```json
{
  "exports": [
    {
      "id": "...",
      "url": "https://example.com",
      "section": "academics",
      "state": "completed",
      "object_key": "exports/academics/....pdf",
      "created_at": "2026-07-28T00:00:00Z",
      "completed_at": "2026-07-28T00:00:45Z"
    }
  ],
  "count": 1,
  "total": 134,
  "limit": 20,
  "offset": 0
}
```

---

## `GET /exports/:id` — Get Export Status

**Response — 200 OK**
```json
{
  "id": "...",
  "url": "https://example.com",
  "section": "academics",
  "state": "completed",
  "object_key": "exports/academics/....pdf",
  "created_at": "2026-07-28T00:00:00Z",
  "completed_at": "2026-07-28T00:00:45Z"
}
```

States: `queued` | `processing` | `completed` | `failed`

---

## `GET /exports/:id/pdf` — Download PDF

Returns a **302 redirect** to a pre-signed S3 URL (valid 15 minutes).

> [!note] Dev / LocalStack mode
> When `S3_ENDPOINT` is set, returns the PDF bytes directly (200 OK, `application/pdf`) because LocalStack pre-signed URLs are not externally accessible.

**Response — 302 Found (production)**
```
Location: https://bucket.s3.amazonaws.com/exports/section/id.pdf?X-Amz-...
```

**Response — 200 OK (dev/LocalStack)**
```
Content-Type: application/pdf
Content-Disposition: inline; filename="<id>.pdf"
<binary PDF>
```

**Error Responses**

| Status | Body | Cause |
|--------|------|-------|
| 404 | `{"error":"export not found"}` | Unknown ID |
| 409 | `{"error":"PDF is not available until the export is completed"}` | Job not yet completed |
| 502 | `{"error":"could not retrieve PDF from storage"}` | S3 error |

---

## Webhook Callback

When `callback_url` is provided in `POST /exports`, the service POSTs the final `exportStatus` to that URL once the job reaches `completed` or `failed`.

**Callback Request**
```http
POST https://myapp.com/webhooks/export HTTP/1.1
Content-Type: application/json

{
  "id": "...",
  "url": "https://example.com/report",
  "section": "academics",
  "state": "completed",
  "object_key": "exports/academics/....pdf",
  "created_at": "2026-07-28T00:00:00Z",
  "completed_at": "2026-07-28T00:00:45Z"
}
```

- One retry after 5 seconds if the first attempt fails.
- Timeout: 10 seconds per attempt.
- Callback failures are logged but do not affect job state.

---

> [!summary] Change Summary
> Added `callback_url` to POST /exports. Added `limit`/`offset`/`total` to GET /exports list. Changed GET /exports/:id/pdf to 302 pre-signed redirect (with LocalStack fallback).

**Tags:** 
**Commit:** 
**PR / Branch:** 
