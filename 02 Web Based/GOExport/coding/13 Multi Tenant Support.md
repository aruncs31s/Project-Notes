---
created: 2026-07-28
tags:
  - goexport
  - coding
  - multitenancy
  - security
status: draft
feature: Multi Tenant Support
type: coding-note
---

# 13 Multi Tenant Support

## Overview

> [!abstract] TL;DR
> Add multi-tenant and user-based isolation to `goexport`. Configurable via `MULTI_TENANT_ENABLED`. When enabled, requests require `X-Tenant-ID` and `X-User-ID` headers, access to exports is strictly scoped, and a new `GET /exports/my` endpoint returns user-specific exports.

## Why / Motivation

In enterprise and SaaS environments, multiple institutes or tenants share a single `goexport` instance. Each user in an institute must only be able to view and download their own generated PDFs. Enabling multi-tenancy enforces strong isolation while keeping the feature completely optional for single-tenant deployments.

## Design Decisions

- **Header-based Context**: Context is passed via HTTP headers (`X-Tenant-ID` and `X-User-ID`).
- **Configurable Mode**: Toggle via `MULTI_TENANT_ENABLED` environment variable (default `false`). When `false`, multi-tenancy checks are bypassed for full backward compatibility.
- **Strict Isolation**: When `MULTI_TENANT_ENABLED=true`:
  - `POST /exports` requires both headers.
  - `GET /exports/:id` and `GET /exports/:id/pdf` check that `TenantID` and `UserID` match the caller's headers. Returns `403 Forbidden` on mismatch.
  - `GET /exports/my` lists exports matching caller's `TenantID` and `UserID`.
- **Database Schema**:
  - `tenant_id` and `user_id` columns in SQLite `export_statuses` table.
  - Indexes on `(tenant_id, user_id, created_at DESC)` for fast queries.
- **S3 Keying**:
  - Format: `exports/{tenant_id}/{section}/{id}.pdf` when `tenant_id` is present, otherwise fallback to `exports/{section}/{id}.pdf`.

## Implementation Plan

### Files to Change / Create

| File | Action | Notes |
|------|--------|-------|
| `internal/config/initializers.go` | **MODIFY** | Add `MultiTenantEnabled` config field |
| `errors.go` | **MODIFY** | Add `ErrMissingTenantID`, `ErrMissingUserID`, `ErrAccessDenied` |
| `export.go` | **MODIFY** | Add `TenantID` and `UserID` to `exportJob` and `exportStatus` |
| `status_store.go` | **MODIFY** | Add `ListByUser` to `StatusStore` interface |
| `store_mem.go` | **MODIFY** | Implement `ListByUser` in `memStatusStore` |
| `store_sqlite.go` | **MODIFY** | Update schema, queries, `ListByUser` implementation |
| `http.go` | **MODIFY** | Add context extraction, header checks, `GET /exports/my`, security guards |
| `export_test.go` | **MODIFY** | Add multi-tenant unit tests |
| `docs/integration.md` | **MODIFY** | Document `GET /exports/my` and multi-tenant headers |

### New Dependencies

None.

## Testing

- Test `POST /exports` with `MULTI_TENANT_ENABLED=true` (accepts with headers, fails 400 without).
- Test `GET /exports/my` returns caller's exports only.
- Test `GET /exports/:id` and `GET /exports/:id/pdf` block access across tenants/users (`403 Forbidden`).
- Test backward compatibility when `MULTI_TENANT_ENABLED=false`.

---

> [!summary] Change Summary
> Implemented configurable multi-tenant and user-based isolation controlled by `MULTI_TENANT_ENABLED`. Added `TenantID` and `UserID` fields to `ExportJob` and `ExportStatus`. Extended `StatusStore` interface and implementations (`memStatusStore`, `sqliteStatusStore`) with compound index `(tenant_id, user_id)` and `ListByUser` / `CountByUser` methods. Added `ExtractUserContext` header parser (`X-Tenant-ID`, `X-User-ID`), `GET /exports/my` endpoint, and strict `403 Forbidden` ownership guards on `GET /exports/:id` and `GET /exports/:id/pdf`. Added multi-tenant integration tests to `routes_test.go`.

**Tags:** 
**Commit:** 
**PR / Branch:** 
