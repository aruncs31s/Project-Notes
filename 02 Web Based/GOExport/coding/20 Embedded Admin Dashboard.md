# 20 Embedded Admin Dashboard

> [!summary] Change Summary
> **Feature**: Moved the admin dashboard templates and API endpoints directly into the main `goexport` service (served under `/admin` on the same port `:8080`). Deleted the standalone `cmd/admin` binary.
> **Status**: ✅ Implemented — verification tests pass
> Tags:
> Commit:

## Overview

Embedding the admin panel into the main web service resolves SQLite database access constraints in containerized cloud environments like Render. Previously, running a separate binary meant either sharing a local SQLite file (which container environments do not support out of the box) or developing network APIs. Exposing `/admin` directly on the main service solves this cleanly.

## Key Design Changes

- **Path**: `/admin` (Dashboard) and `/admin/api/*` (Admin API) on the primary service.
- **Port**: Shared on `:8080` (or `HTTP_ADDR`). No separate port mapping is needed.
- **Templates**: Moved from `cmd/admin/templates` to `internal/templates`.
- **Security**: The `/admin` routes require the environment variable `ADMIN_PASSWORD` to be set. If not set, accessing `/admin` returns `403 Forbidden`.

## Admin Handlers on Main Router

`internal/routes/routes.go` now:
1. Instantiates an in-memory thread-safe `sessionStore` for the admin portal session tokens.
2. Registers routes under the `/admin` prefix.
3. Implements authentication middlewares:
   - `requireAdminSession`: Returns `401 Unauthorized` for AJAX JSON requests.
   - `requireAdminSessionPage`: Redirects to `/admin` login page for SSR page requests.
