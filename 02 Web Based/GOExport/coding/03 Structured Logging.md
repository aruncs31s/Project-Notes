---
created: 2026-07-28
tags:
  - goexport
  - coding
  - logging
  - observability
status: draft
feature: Structured Logging
type: coding-note
---

# 03 Structured Logging

## Overview

> [!abstract] TL;DR
> Replace all `log.Printf` / `log.Fatal` calls with `log/slog` (stdlib since Go 1.21) so every log line is machine-parseable JSON in production and human-friendly text in dev.

## Why / Motivation

`log.Printf` produces unstructured strings. In production (log aggregation, Loki, CloudWatch) you can't filter "all logs for job_id X" or "all errors from the broker" without regex hacks. `slog` gives key-value structured output with zero new dependencies.

## Design Decisions

- **Stdlib only**: `log/slog` — no Zap, no Logrus. Keeps the dep graph clean.
- **Text handler in dev, JSON in prod**: detect via `LOG_FORMAT=json` env var.
- **Global logger set once in `main`**: `slog.SetDefault(logger)` — all packages call `slog.Info(...)`.
- **Job ID in context**: use `slog.With("job_id", job.ID)` when processing a job.
- **Replace `log.Fatal`** with `slog.Error` + `os.Exit(1)` for consistency.

## Implementation Plan

### Files to Change / Create

| File | Action | Notes |
|------|--------|-------|
| `cmd/main.go` | **MODIFY** | Init slog handler based on `LOG_FORMAT` |
| `export.go` | **MODIFY** | Replace log calls; add `job_id` to context logs |
| `broker.go` | **MODIFY** | Replace log calls |
| `internal/config/initializers.go` | **MODIFY** | Add `LogFormat` field |

### New Dependencies

None — `log/slog` is stdlib.

### Interface / API Contract

```go
// Set once in main():
slog.SetDefault(slog.New(handler))

// Usage everywhere:
slog.Info("PDF worker stopped", "error", err)
slog.Error("render failed", "job_id", job.ID, "url", job.URL, "error", err)
```

## Implementation Notes

- Dev handler: `slog.NewTextHandler(os.Stdout, &slog.HandlerOptions{Level: slog.LevelDebug})`
- Prod handler: `slog.NewJSONHandler(os.Stdout, &slog.HandlerOptions{Level: slog.LevelInfo})`
- Reconnect log: `slog.Warn("RabbitMQ disconnected, retrying", "attempt", n, "backoff_s", delay)`
- Job completion: `slog.Info("job completed", "job_id", id, "section", section, "key", key, "duration_ms", elapsed)`

## Testing

No unit tests needed. Verify manually:
```
LOG_FORMAT=json go run . 2>&1 | jq .
```

---

> [!summary] Change Summary
> Implemented structured logging with log/slog. Replaced log calls in main.go and added job_id logging in export.go process and ConsumeSequentially.

**Tags:** 
**Commit:** 
**PR / Branch:** 
