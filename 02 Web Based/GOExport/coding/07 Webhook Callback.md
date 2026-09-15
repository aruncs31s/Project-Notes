---
created: 2026-07-28
tags:
  - goexport
  - coding
  - webhook
  - async
status: draft
feature: Webhook Callback
type: coding-note
---

# 07 Webhook Callback

## Overview

> [!abstract] TL;DR
> Accept an optional `callback_url` in `POST /exports`. When a job completes or fails, POST the final `exportStatus` JSON to that URL, eliminating the need for clients to poll.

## Why / Motivation

Clients currently must poll `GET /exports/:id` repeatedly until state transitions to `completed` or `failed`. A job can take 10–90 seconds. This creates unnecessary HTTP traffic and adds latency (polling interval). A push callback solves both.

## Design Decisions

- **Optional field**: `callback_url` is optional. If absent, behaviour is unchanged.
- **Validation**: must be an absolute `http(s)://` URL (same rules as job URL).
- **Fire-and-forget goroutine**: the callback POST happens in a background goroutine after state transition — it does not block the consume loop.
- **One retry**: if the POST fails, retry once after 5s. Beyond that, log and move on.
- **Body**: full `exportStatus` JSON, same shape as `GET /exports/:id`.
- **Timeout**: 10s per callback attempt.
- **Store callback_url in the job**: add to `exportJob` struct so it survives the queue round-trip.

## Implementation Plan

### Files to Change / Create

| File | Action | Notes |
|------|--------|-------|
| `export.go` | **MODIFY** | Add `CallbackURL` to `exportJob` + `exportStatus`; fire callback after process/failure |
| `http.go` | **MODIFY** | Accept `callback_url` in request, validate, pass to job |

### New Dependencies

None — uses stdlib `net/http`.

### Interface / API Contract

```
POST /exports
{
  "url": "https://example.com/report",
  "section": "academics",
  "callback_url": "https://myapp.com/webhooks/export"   // optional
}
```

Callback payload (POSTed to `callback_url` on completion):
```json
{
  "id": "...",
  "url": "...",
  "section": "...",
  "state": "completed",
  "object_key": "exports/academics/....pdf",
  "created_at": "...",
  "completed_at": "..."
}
```

## Implementation Notes

- `fireCallback(url string, status exportStatus)` — standalone func, not a method.
- Use `http.NewRequestWithContext` with a 10s timeout context.
- Set `Content-Type: application/json` header on callback POST.
- Log callback result: `slog.Info("callback delivered", "url", url, "status_code", resp.StatusCode)`.
- Log warning on failure, do not alter job state.

## Testing

- Unit test: set up an `httptest.Server` as the callback target; verify it receives the correct payload when `process()` completes.

---

> [!summary] Change Summary
> Added `CallbackURL` field to `exportJob` and `exportStatus` structs. Accepted and validated in `POST /exports` handler. `fireCallback()` function POSTs the final status JSON to the callback URL with a 10s timeout and one retry after 5s. Called from `process()` and `setFailure()` in goroutines.

**Tags:** 
**Commit:** 
**PR / Branch:** 
