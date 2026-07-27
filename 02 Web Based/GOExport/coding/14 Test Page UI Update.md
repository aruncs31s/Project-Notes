---
created: 2026-07-28
tags:
  - goexport
  - coding
  - ui
  - testing
status: draft
feature: Test Page UI Update
type: coding-note
---

# 14 Test Page UI Update

## Overview

> [!abstract] TL;DR
> Update `tests/index.html` with a modern, responsive UI supporting all new service features: multi-tenant headers (`X-Tenant-ID`, `X-User-ID`), section tags, callback URLs, status polling, PDF download links, `GET /exports/my`, and health/metrics diagnostic controls.

## Why / Motivation

The original `tests/index.html` only supported basic URL submission and polling without section tags, callback URLs, multi-tenant headers, PDF downloads, or user-scoped listing (`GET /exports/my`). Updating the test page allows full manual verification of all features directly from the browser test server (`go run ./tests/server.go`).

## Design & Features

1. **Modern Premium Design System**:
   - Clean dark-mode inspired/sleek theme with CSS variables, smooth inputs, cards, and responsive layout.
2. **Configuration & Multi-Tenancy Inputs**:
   - API Base URL (default `http://localhost:8080`)
   - Section input (default `general`)
   - Callback URL input (optional)
   - Tenant ID (`X-Tenant-ID`) and User ID (`X-User-ID`) inputs
3. **HTML Upload & Preview**:
   - File selector, upload button, and live preview `iframe`
4. **Export Operations**:
   - **Queue PDF Export**: sends `POST /exports` with section, callback URL, and multi-tenant headers
   - **Check Status**: polls `GET /exports/:id` with multi-tenant headers
   - **Download PDF**: opens/downloads `GET /exports/:id/pdf` with multi-tenant headers
5. **List Operations**:
   - **List All Exports**: calls `GET /exports` with section filter and pagination
   - **List My Exports**: calls `GET /exports/my` with `X-Tenant-ID` and `X-User-ID` headers
6. **Diagnostics**:
   - Quick buttons for `GET /healthz`, `GET /readyz`, and `GET /metrics`

## Implementation Plan

### Files to Change / Create

| File | Action | Notes |
|------|--------|-------|
| `tests/index.html` | **MODIFY** | Full HTML/CSS/JS rewrite with modern UI and complete API support |

---

> [!summary] Change Summary
> Redesigned `tests/index.html` with a modern, dark-themed UI (Inter font, cards, styled inputs, and JetBrains Mono code log). Added support for multi-tenant headers (`X-Tenant-ID`, `X-User-ID`), section tags, callback URLs, status polling, blob/pre-signed PDF downloading, `GET /exports/my`, `GET /exports`, and quick health/readiness/metrics diagnostic triggers.

**Tags:** 
**Commit:** 
**PR / Branch:**
