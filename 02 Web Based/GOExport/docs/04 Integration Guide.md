---
created: 2026-07-28
tags:
  - goexport
  - docs
  - integration
  - guide
status: in-progress
type: docs-note
---

# 04 Integration Guide

## Overview

> [!abstract] TL;DR
> A comprehensive, developer-facing guide covering every aspect of integrating with the GOExport HTTP API — from first request to production patterns.

## Why / Motivation

The README and existing `docs/operations.md` explain the service from an operator's perspective. There is no guide aimed at the **caller** — the developer writing code that sends jobs to goexport and consumes the results. This document fills that gap.

## Scope

The integration guide covers:

1. Quick start (5-minute first PDF)
2. Full async workflow — submit → poll OR submit → webhook → download
3. Complete API reference with request/response shapes
4. Error handling and all HTTP status codes
5. Sections — organising and filtering exports
6. Pagination
7. Webhook callbacks (receiving and verifying)
8. Downloading PDFs — presign redirect vs dev-mode streaming
9. Health & readiness probes
10. Metrics scraping
11. curl examples for every endpoint
12. Production integration tips (idempotency, retry, timeouts)

## Output

- `docs/integration.md` — in the project repo, linked from README
- This note (`docs/04 Integration Guide.md`) — in the Obsidian vault

## Implementation Plan

### Files to Change / Create

| File | Action | Notes |
|------|--------|-------|
| `docs/integration.md` | **CREATE** | The full guide |
| `README.md` | **MODIFY** | Add link to integration guide |

### New Dependencies

None.

## Implementation Notes

Guide structure decided while writing:

1. **Overview** — what the service does, async-only mental model
2. **Base URL & auth** — host, no auth by default, CORS note
3. **Quick start** — two curl commands to go from zero to PDF
4. **Core workflow** — two paths: poll and webhook
5. **API reference** — one section per endpoint with full examples
6. **Error reference** — table of all error strings + status codes
7. **Sections** — naming rules, filtering
8. **Pagination** — envelope fields, defaults, limits
9. **Webhooks** — receiving, payload shape, retry behaviour
10. **PDF download** — prod vs dev mode
11. **Health & metrics** — probes, Prometheus queries
12. **Code examples** — shell (curl) for every flow
13. **Production tips** — timeout strategy, idempotency, SSRF warning

---

> [!summary] Change Summary
> Created `docs/integration.md` — full developer integration guide. Linked from `README.md`. Covers all 7 endpoints, both polling and webhook workflows, all error codes, pagination, sections, and production tips.

**Tags:** 
**Commit:** 
**PR / Branch:** 
