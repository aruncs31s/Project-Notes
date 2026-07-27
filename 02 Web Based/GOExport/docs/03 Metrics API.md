---
created: 2026-07-28
tags:
  - goexport
  - docs
  - api
  - metrics
  - prometheus
status: draft
type: api-doc
---

# 03 Metrics API

## `GET /metrics` — Prometheus Metrics

Returns Prometheus text format metrics. Scrape this endpoint with a Prometheus server or Grafana Agent.

**Request**
```http
GET /metrics HTTP/1.1
```

**Response — 200 OK**
```
Content-Type: text/plain; version=0.0.4; charset=utf-8
```

## Custom Metrics

| Metric Name | Type | Labels | Description |
|-------------|------|--------|-------------|
| `goexport_jobs_submitted_total` | Counter | `section` | Total jobs successfully enqueued to RabbitMQ |
| `goexport_jobs_completed_total` | Counter | `section` | Total jobs that reached `completed` state |
| `goexport_jobs_failed_total` | Counter | `section`, `reason` | Total failed jobs. `reason`: `render`, `upload`, `broker` |
| `goexport_render_duration_seconds` | Histogram | `section` | Time taken by chromedp to render a PDF |
| `goexport_upload_duration_seconds` | Histogram | `section` | Time taken to upload PDF to S3 |

## Standard Go Runtime Metrics

The following are registered automatically by `prometheus/client_golang`:

- `go_goroutines` — current goroutine count
- `go_gc_duration_seconds` — GC pause durations
- `process_resident_memory_bytes` — RSS
- `process_cpu_seconds_total` — CPU time

## Example Prometheus Scrape Config

```yaml
scrape_configs:
  - job_name: goexport
    static_configs:
      - targets: ['localhost:8080']
    metrics_path: /metrics
    scrape_interval: 15s
```

## Example Grafana Queries

```promql
# P95 render latency by section
histogram_quantile(0.95, rate(goexport_render_duration_seconds_bucket[5m]))

# Job failure rate
rate(goexport_jobs_failed_total[5m])

# Completion throughput
rate(goexport_jobs_completed_total[1m])
```

---

> [!summary] Change Summary
> Added `/metrics` endpoint. Exposes 5 custom metrics (2 counters, 3 histograms) plus standard Go runtime metrics.

**Tags:** 
**Commit:** 
**PR / Branch:** 
