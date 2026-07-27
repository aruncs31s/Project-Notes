---
created: 2026-07-28
tags:
  - goexport
  - coding
  - cleanup
  - ttl
status: draft
feature: Status TTL Cleanup
type: coding-note
---

# 08 Status TTL Cleanup

## Overview

> [!abstract] TL;DR
> Add a background goroutine that periodically deletes `completed` and `failed` statuses older than a configurable TTL (default 24h) to prevent unbounded growth.

## Why / Motivation

`exportService.statuses` (and after feature 04, the SQLite DB) grows forever — every export job ever submitted stays in memory/disk. In a busy system with thousands of daily exports this will:
- OOM the process (in-memory case)
- Grow the SQLite file without bound
- Slow down `GET /exports` list queries

## Design Decisions

- **TTL scope**: only `completed` and `failed` states. `queued` and `processing` are never deleted.
- **Configurable**: `STATUS_TTL` env var, default `24h`, parsed with `time.ParseDuration`.
- **Cleanup interval**: `TTL / 4` (every 6h for 24h TTL). No need for a separate env var.
- **SQLite**: `DELETE FROM export_statuses WHERE state IN ('completed','failed') AND created_at < ?`
- **Graceful shutdown**: cleanup goroutine respects `ctx.Done()`.

## Implementation Plan

### Files to Change / Create

| File | Action | Notes |
|------|--------|-------|
| `store/status.go` | **MODIFY** | Add `DeleteBefore(ctx, cutoff time.Time) (int64, error)` to `StatusStore` |
| `export.go` | **MODIFY** | Add `StartCleanup(ctx, ttl)` method |
| `internal/config/initializers.go` | **MODIFY** | Add `StatusTTL time.Duration` |
| `cmd/main.go` | **MODIFY** | Launch cleanup goroutine |

### New Dependencies

None.

### Interface / API Contract

```go
// StatusStore addition
DeleteBefore(ctx context.Context, cutoff time.Time) (deleted int64, err error)

// exportService
func (s *exportService) StartCleanup(ctx context.Context, ttl time.Duration)
```

## Implementation Notes

- Cleanup goroutine:
  ```go
  ticker := time.NewTicker(ttl / 4)
  defer ticker.Stop()
  for { select { case <-ctx.Done(): return case <-ticker.C: s.store.DeleteBefore(ctx, time.Now().Add(-ttl)) } }
  ```
- Log how many rows were deleted each cycle: `slog.Info("cleanup", "deleted", n)`.
- For the in-memory store: iterate `statuses` map, delete entries with matching state and age.

## Testing

- Unit test: seed store with 5 old completed + 2 recent + 1 queued; call `DeleteBefore`; verify only old completed ones are gone.

---

> [!summary] Change Summary
> _Fill in after implementation._

**Tags:** 
**Commit:** 
**PR / Branch:** 
