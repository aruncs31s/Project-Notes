---
created: 2026-07-28
tags:
  - goexport
  - coding
  - persistence
  - sqlite
status: draft
feature: Status Persistence
type: coding-note
---

# 04 Status Persistence

## Overview

> [!abstract] TL;DR
> Replace the in-memory `map[string]exportStatus` with a `StatusStore` interface backed by SQLite (embedded, no extra service). Status survives server restarts.

## Why / Motivation

Currently all job statuses live in a `sync.RWMutex`-protected map in `exportService`. A server restart clears every status. Any job that was `queued` or `processing` in RabbitMQ will be re-processed by the worker, but `GET /exports/:id` will return 404 — clients have no way to track those jobs.

## Design Decisions

- **Interface-first**: define `StatusStore` interface; swap in-memory vs SQLite via config.
- **SQLite via `modernc.org/sqlite`**: pure Go, no CGO, no extra service. DB file path configurable via `SQLITE_PATH` env (defaults to `./goexport.db`).
- **Schema**: single `export_statuses` table. Indexes on `section` and `created_at` for list queries.
- **Keep in-memory impl**: for tests — `fakeStore` / `memStore` implements `StatusStore`.
- **Migrations**: use plain `CREATE TABLE IF NOT EXISTS` (no migration library needed at this scale).

## Implementation Plan

### Files to Change / Create

| File | Action | Notes |
|------|--------|-------|
| `store/status.go` | **CREATE** | `StatusStore` interface + SQLite impl |
| `store/mem_status.go` | **CREATE** | In-memory impl for tests |
| `export.go` | **MODIFY** | Replace `map + mu` with `StatusStore` calls |
| `internal/config/initializers.go` | **MODIFY** | Add `SQLitePath` field |
| `cmd/main.go` | **MODIFY** | Init SQLite store, pass to service |
| `export_test.go` | **MODIFY** | Use `memStore` instead of bare map |

### New Dependencies

```
go get modernc.org/sqlite
```

### Interface / API Contract

```go
type StatusStore interface {
    Set(ctx context.Context, s exportStatus) error
    Get(ctx context.Context, id string) (exportStatus, bool, error)
    List(ctx context.Context, section string, limit, offset int) ([]exportStatus, error)
    Delete(ctx context.Context, id string) error
}
```

## Implementation Notes

- SQLite WAL mode for better concurrent reads: `PRAGMA journal_mode=WAL`
- Schema:
```sql
CREATE TABLE IF NOT EXISTS export_statuses (
    id           TEXT PRIMARY KEY,
    url          TEXT NOT NULL,
    section      TEXT NOT NULL DEFAULT 'general',
    state        TEXT NOT NULL,
    object_key   TEXT,
    error        TEXT,
    created_at   DATETIME NOT NULL,
    completed_at DATETIME
);
CREATE INDEX IF NOT EXISTS idx_section ON export_statuses(section);
CREATE INDEX IF NOT EXISTS idx_created_at ON export_statuses(created_at DESC);
```
- Use `database/sql` with `modernc.org/sqlite` driver tag `sqlite`.

## Testing

- Existing tests use `fakePublisher` — update to use `memStore` for `StatusStore`.
- Add a SQLite store integration test that creates a temp DB file and verifies Set/Get/List.

---

> [!summary] Change Summary
> Defined `StatusStore` interface in `status_store.go`. Created `store_mem.go` (in-memory, for tests) and `store_sqlite.go` (production, `modernc.org/sqlite`). SQLite uses WAL mode, upsert-on-conflict, and indexes on `section` and `created_at`. `export.go` refactored to use `StatusStore` — removed `sync.RWMutex` map. `NewSQLiteStore` opens/creates the DB at `SQLITE_PATH`. All tests updated to use `newMemStore()`.

**Tags:** 
**Commit:** 
**PR / Branch:** 
