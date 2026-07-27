---
created: 2026-07-28
tags:
  - goexport
  - coding
  - api
  - pagination
status: draft
feature: Pagination on List
type: coding-note
---

# 10 Pagination on List

## Overview

> [!abstract] TL;DR
> Add `limit` and `offset` query parameters to `GET /exports` so the list endpoint doesn't return unbounded data.

## Why / Motivation

`GET /exports` currently returns the entire statuses map in one response. With thousands of jobs this becomes a large JSON payload and a slow query. Pagination is the standard fix.

## Design Decisions

- **Offset pagination** (not cursor): simpler to implement and sufficient for this scale. Clients can bookmark `offset`.
- **Defaults**: `limit=50`, `offset=0`. Max limit capped at `200` to prevent abuse.
- **Response envelope**: add `total`, `limit`, `offset` to response alongside `exports` and `count`.
- **Sorting**: already sorts by `created_at DESC` — keep that.

## Implementation Plan

### Files to Change / Create

| File | Action | Notes |
|------|--------|-------|
| `http.go` | **MODIFY** | Parse `limit`/`offset` query params, validate, pass to service |
| `export.go` | **MODIFY** | `listStatuses` accepts `limit, offset int` |
| `store/status.go` | **MODIFY** | `List` already has `limit, offset` (from feature 04) |

### New Dependencies

None.

### Interface / API Contract

```
GET /exports?section=academics&limit=20&offset=40
200 OK
{
  "exports": [...],
  "count": 20,
  "total": 134,
  "limit": 20,
  "offset": 40
}
```

Error cases:
- `limit` < 1 or > 200 → 400 Bad Request
- `offset` < 0 → 400 Bad Request

## Implementation Notes

- Parse with `strconv.Atoi`, return 400 on parse error.
- `total` comes from a `Count(ctx, section) (int, error)` method on `StatusStore`.
- The in-memory store counts by iterating the map (acceptable — it's only used in tests).

## Testing

- Unit test: seed 10 statuses, request `limit=3&offset=6`, verify 3 items returned with correct `total=10`.
- Unit test: `limit=300` → 400.

---

> [!summary] Change Summary
> _Fill in after implementation._

**Tags:** 
**Commit:** 
**PR / Branch:** 
