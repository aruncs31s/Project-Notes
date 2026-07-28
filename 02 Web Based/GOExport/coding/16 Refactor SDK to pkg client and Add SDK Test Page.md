---
created: 2026-07-28
tags:
  - goexport
  - coding
  - sdk
  - pkg
  - testing
status: draft
feature: Refactor SDK to pkg/client and Add SDK Test Page
type: coding-note
---

# 16 Refactor SDK to pkg/client and Add SDK Test Page

## Overview

> [!abstract] TL;DR
> Move the Go Client SDK to `pkg/client` following standard Go project conventions, and create an interactive web test application in `pkg/client/example/` to test the Go SDK driver end-to-end.

## Why / Motivation

1. **Standard Go Project Layout**: Publicly importable packages belong under `pkg/` (`github.com/aruncs31s/goexport/pkg/client`). This makes it clear to external developers that `pkg/client` is the official client library for `goexport`.
2. **Dedicated SDK Test Harness**: Having an interactive test application in `pkg/client/example/` allows developers to test the Go SDK methods (`ExportURL`, `CreateExport`, `GetStatus`, `DownloadPDF`, `ListMyExports`) directly against a running `goexport` server.

## Design Decisions

- **Package location**: `pkg/client`
- **Example app**:
  - `pkg/client/example/main.go`: Web server on port `8091` that uses the `pkg/client` Go driver.
  - `pkg/client/example/index.html`: Interactive web console calling `pkg/client/example` endpoints to exercise the SDK.

## Implementation Plan

### Files to Change / Create

| File | Action | Notes |
|------|--------|-------|
| `pkg/client/client.go` | **CREATE** | Move `client/client.go` to `pkg/client/` |
| `pkg/client/client_test.go` | **CREATE** | Move `client/client_test.go` to `pkg/client/` |
| `client/client.go` | **DELETE** | Remove old file location |
| `client/client_test.go` | **DELETE** | Remove old file location |
| `pkg/client/example/main.go` | **CREATE** | SDK test server application |
| `pkg/client/example/index.html` | **CREATE** | Interactive UI for testing the Go SDK |
| `docs/integration.md` | **MODIFY** | Update import path to `pkg/client` |

---

> [!summary] Change Summary
> Refactored Go Client SDK location to `pkg/client` (`github.com/aruncs31s/goexport/pkg/client`) following standard Go project conventions. Built an interactive SDK test harness in `pkg/client/example/` consisting of a Go web server (`main.go` listening on port `8091`) and a modern web UI (`index.html`) that invokes `client.ExportURL`, `client.CreateExport`, `client.GetStatus`, `client.DownloadPDF`, and `client.ListMyExports` end-to-end. Updated all documentation references.

**Tags:** 
**Commit:** 
**PR / Branch:** 
