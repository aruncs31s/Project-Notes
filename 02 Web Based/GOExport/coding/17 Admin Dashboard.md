# 17 Admin Dashboard

> [!summary] Change Summary
> **Feature**: Standalone admin dashboard binary (`cmd/admin`) with an embedded dark-mode SPA for managing exports, tokens, and cleanup.
> **Status**: Implementation in progress
> Tags:
> Commit:

## Overview

A separate Go binary that opens the same SQLite database as the main goexport service and serves a self-contained admin web UI. The dashboard provides full visibility into all exports across tenants/users, token management, stats at a glance, and bulk cleanup.

## Architecture

```
cmd/admin/main.go         ← entry point, Gin router, session auth
cmd/admin/static/
  index.html              ← embedded SPA (dark glass-morphism)
```

- Port: `:8081` (env `ADMIN_HTTP_ADDR`)
- Auth: separate `ADMIN_PASSWORD` env var, session cookie (not the API token)
- DB: opens `SQLITE_PATH` in WAL mode (safe concurrent with main service)
- No dependency on RabbitMQ, S3, or Chrome

## New Config Fields

| Env Var | Default | Purpose |
|---|---|---|
| `ADMIN_HTTP_ADDR` | `:8081` | Listen address for the admin dashboard |
| `ADMIN_PASSWORD` | *(required)* | Admin login password |

## New Storage Methods

- `ListFiltered(ctx, ListFilter) ([]ExportStatus, int, error)` — multi-filter list with count
- `Delete(ctx, id) error` — hard-delete a single export record
- `Stats(ctx) (map[string]int, error)` — counts grouped by state
- `GetAdminConfig(ctx, key) (string, bool, error)` — read from `admin_config` table
- `SetAdminConfig(ctx, key, value) error` — write to `admin_config` table

## Admin API Routes

| Method | Path | Description |
|---|---|---|
| `POST` | `/admin/login` | Validate password, set session cookie |
| `POST` | `/admin/logout` | Clear session cookie |
| `GET` | `/admin/api/exports` | List with filters: section, tenant_id, user_id, state, limit, offset |
| `DELETE` | `/admin/api/exports/:id` | Delete single export |
| `DELETE` | `/admin/api/exports` | Bulk delete by state and/or age |
| `GET` | `/admin/api/stats` | Counts by state |
| `GET` | `/admin/api/token` | Show current token (masked) |
| `POST` | `/admin/api/token/rotate` | Generate new token, persist in admin_config |

## SPA Features

- Login screen
- Stats overview cards (Total, Queued, Processing, Completed, Failed)
- Exports table with filters, pagination, per-row delete
- Export detail modal
- Token management panel
- Bulk cleanup panel

## Token Rotation Strategy

- New token is persisted in `admin_config` table under key `auth_token`
- Main service reads from `admin_config` at **startup** (overrides env var)
- After rotating, restart main service to pick up the new token

## Docker Compose

Added as an optional `admin` profile:
```bash
docker compose --profile admin up
```

## Notes

- WAL mode is already enabled by the main service schema; safe for concurrent reads
- Session store is in-memory (`map[string]time.Time`); admin binary restart clears all sessions
- Admin binary does not start a RabbitMQ consumer or Chrome renderer
