---
created: 2026-07-28
tags:
  - goexport
  - coding
  - chrome
  - engines
status: draft
feature: Shared Chrome Allocator
type: coding-note
---

# 01 Shared Chrome Allocator

## Overview

> [!abstract] TL;DR
> Replace per-job `chromedp.NewContext` calls with a single persistent Chrome process managed by `chromedp.NewExecAllocator`, and move all rendering code into the `engines/` package.

## Why / Motivation

Currently `renderPDF` calls `chromedp.NewContext(ctx)` without a parent allocator, which **starts a fresh Chrome process for every single job**. This means:
- ~1–2 second cold start per job (Chrome binary launch)
- High memory churn (each job spawns and destroys a process)
- No tab reuse or Chrome process pooling

A persistent allocator solves all three by keeping Chrome running between jobs and only opening a new tab per job.

The `engines/` directory was already created as a placeholder — this is the right moment to move rendering there and establish the pattern for future backends.

## Design Decisions

- **One allocator, many tabs**: `chromedp.NewExecAllocator` is called once at startup; each `renderPDF` call opens a new tab (child context) under it.
- **Move, don't copy**: `renderPDF` moves from `export.go` to `engines/chromedp.go`. The `pdfRenderer` function type stays in the root package for backward compat.
- **Constructor pattern**: `engines.NewChromeRenderer()` returns a `pdfRenderer`-compatible function and a `cancel` func for cleanup.
- **`--no-sandbox` flag**: needed for running inside Docker/CI without root.

## Implementation Plan

### Files to Change / Create

| File | Action | Notes |
|------|--------|-------|
| `engines/chromedp.go` | **CREATE** | Persistent allocator + `NewChromeRenderer()` |
| `export.go` | **MODIFY** | Remove `renderPDF`, remove chromedp imports |
| `cmd/main.go` | **MODIFY** | Call `engines.NewChromeRenderer()`, pass to `NewExportService` |

### New Dependencies

None — `chromedp` is already in `go.mod`.

### Interface / API Contract

```go
// engines/chromedp.go
package engines

// NewChromeRenderer starts a persistent Chrome process and returns
// a pdfRenderer-compatible function and a shutdown cancel func.
func NewChromeRenderer() (render func(context.Context, string) ([]byte, error), cancel func())
```

## Implementation Notes

- Allocator options: `chromedp.NoFirstRun`, `chromedp.NoDefaultBrowserCheck`, `chromedp.Headless`, `chromedp.DisableGPU`, `chromedp.Flag("no-sandbox", true)`
- The allocator context must outlive all render calls — hold it in `main()` and defer `cancel()`
- Each render call does: `chromedp.NewContext(allocCtx)` → new tab, not new process

## Testing

- Existing unit tests mock the renderer — no change needed there.
- Manual: run `go run .` and POST an export; check logs show single Chrome process with `ps aux | grep chrome`.

---

> [!summary] Change Summary
> _Fill in after implementation._

**Tags:** 
**Commit:** 
**PR / Branch:** 
