---
created: 2026-07-28
tags:
  - goexport
  - coding
  - saas
  - sdk
  - auth
status: draft
feature: SaaS Authentication and Client SDK
type: coding-note
---

# 15 SaaS Authentication and Client SDK

## Overview

> [!abstract] TL;DR
> Implement API Token authentication middleware (`AUTH_ENABLED`, `AUTH_TOKEN`), a dedicated Tenant/User Context extraction middleware, and a Go Client SDK (`client/`) that mimics standard drivers like AWS S3, Redis, and GORM.

## Why / Motivation

To operate `goexport` as a SaaS platform, calling services require a simple, standard client library (driver) that handles URL submission, status polling, token authentication (`Bearer` or `X-API-Key`), tenant isolation headers (`X-Tenant-ID`, `X-User-ID`), and PDF downloading cleanly.

## Design Decisions

- **Token Authentication Middleware**:
  - Controlled by `AUTH_ENABLED` (default `false`) and `AUTH_TOKEN` config variables.
  - Accepts tokens via `Authorization: Bearer <token>` or `X-API-Key: <token>`. Returns `401 Unauthorized` on missing or invalid token.
- **Tenant & User Context Middleware**:
  - Standardized Gin middleware that extracts `X-Tenant-ID` (institution ID) and `X-User-ID` (user ID) headers.
  - Enforces presence when `MULTI_TENANT_ENABLED=true`.
- **Go Client SDK (`client/`)**:
  - Clean client struct `client.New(baseURL, token, opts...)`.
  - Supports functional options (`WithTenant`, `WithUser`, `WithHTTPClient`).
  - Provides low-level methods: `CreateExport`, `GetStatus`, `DownloadPDF`, `ListMyExports`.
  - Provides high-level convenience helper: `ExportURL` (submits job, polls until complete, downloads and returns PDF byte slice).

## Implementation Plan

### Files to Change / Create

| File | Action | Notes |
|------|--------|-------|
| `internal/config/initializers.go` | **MODIFY** | Add `AuthEnabled` and `AuthToken` config fields |
| `errors.go` | **MODIFY** | Add `ErrUnauthorized` sentinel error |
| `internal/handlers/http/middleware.go` | **MODIFY** | Implement `RequireAuth` and `ExtractUserContext` middleware |
| `internal/routes/routes.go` | **MODIFY** | Wire auth and context middleware |
| `client/client.go` | **CREATE** | Go Client SDK package |
| `client/client_test.go` | **CREATE** | Unit tests for Client SDK |
| `internal/routes/routes_test.go` | **MODIFY** | Add tests for token authentication middleware |
| `docs/integration.md` | **MODIFY** | Document Client SDK and token auth |

---

> [!summary] Change Summary
> Implemented SaaS Token Authentication middleware (`AUTH_ENABLED`, `AUTH_TOKEN`), supporting `Authorization: Bearer <token>` and `X-API-Key: <token>`. Created dedicated `ExtractUserContext` middleware for `X-Tenant-ID` / `X-Institution-ID` and `X-User-ID`. Built Go Client SDK (`client/`) driver supporting functional options (`WithTenant`, `WithUser`), low-level methods (`CreateExport`, `GetStatus`, `DownloadPDF`, `ListMyExports`), and a single-line high-level driver helper `ExportURL` that submits, polls, and returns PDF bytes.

**Tags:** 
**Commit:** 
**PR / Branch:** 
