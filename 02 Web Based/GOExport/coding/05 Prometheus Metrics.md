---
created: 2026-07-28
tags:
  - goexport
  - coding
  - observability
  - prometheus
status: draft
feature: Prometheus Metrics
type: coding-note
---

# 05 Prometheus Metrics

## Overview

> [!abstract] TL;DR
> Add a `/metrics` endpoint exposing Prometheus counters and histograms for job queue depth, processing latency, S3 upload latency, and failure rates.

## Why / Motivation

Without metrics there is no way to answer: "How long does a PDF take to render?", "How many jobs failed this hour?", "Is the queue backing up?". Prometheus + Grafana is the standard answer for Go services.

## Design Decisions

- **`prometheus/client_golang`**: standard library for Go metrics. Adds ~1MB to binary.
- **Expose on main server** at `/metrics` (not a separate port) for simplicity.
- **Histograms over Gauges for latency**: use `prometheus.NewHistogramVec` for render + upload time.
- **Metrics defined in a `metrics` package** to avoid import cycles.
- **Don't instrument every HTTP path** — Gin's `gin-contrib/pprof` is enough for HTTP; focus metrics on the job pipeline.

## Implementation Plan

### Files to Change / Create

| File | Action | Notes |
|------|--------|-------|
| `metrics/metrics.go` | **CREATE** | Define and register all metrics |
| `export.go` | **MODIFY** | Record render latency, upload latency, job counts |
| `http.go` | **MODIFY** | Add `/metrics` route with `promhttp.Handler()` |

### New Dependencies

```
go get github.com/prometheus/client_golang/prometheus
go get github.com/prometheus/client_golang/prometheus/promhttp
```

### Interface / API Contract

Metrics exposed:

| Metric | Type | Labels | Description |
|--------|------|--------|-------------|
| `goexport_jobs_submitted_total` | Counter | `section` | Jobs successfully enqueued |
| `goexport_jobs_failed_total` | Counter | `section`, `reason` | Jobs that failed (render/upload) |
| `goexport_jobs_completed_total` | Counter | `section` | Jobs completed successfully |
| `goexport_render_duration_seconds` | Histogram | `section` | chromedp render time |
| `goexport_upload_duration_seconds` | Histogram | `section` | S3 PutObject time |

## Implementation Notes

- Register metrics in `metrics.go` using `prometheus.MustRegister`.
- In `export.go process()`, wrap render and upload with `time.Now()` and observe histogram.
- Default Go runtime metrics (GC, goroutines) are registered automatically via `prometheus.DefaultRegisterer`.

## Testing

- No unit tests. Verify manually:
```sh
curl http://localhost:8080/metrics | grep goexport
```

---

> [!summary] Change Summary
> Created `metrics/metrics.go` package with 5 metrics: `JobsSubmitted`, `JobsCompleted`, `JobsFailed` (counters), `RenderDuration`, `UploadDuration` (histograms). All labeled by `section`. Instrumented `export.go process()` with render/upload timings and job counts. Added `/metrics` route via `promhttp.Handler()`. Metrics package imported as side-effect in `cmd/main.go`.

**Tags:** 
**Commit:** 
**PR / Branch:** 
