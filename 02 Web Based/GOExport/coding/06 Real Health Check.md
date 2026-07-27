---
created: 2026-07-28
tags:
  - goexport
  - coding
  - reliability
  - healthcheck
status: draft
feature: Real Health Check
type: coding-note
---

# 06 Real Health Check

## Overview

> [!abstract] TL;DR
> Make `GET /healthz` actually verify RabbitMQ and S3 connectivity instead of always returning 204.

## Why / Motivation

A health check that always returns 200 is worse than useless — load balancers and orchestrators (k8s, ECS) use it to decide whether to send traffic. If RabbitMQ is down the service cannot process any jobs, yet it looks healthy.

## Design Decisions

- **Liveness vs Readiness split**: add two endpoints:
  - `GET /healthz` — liveness: is the process running? (always 204, unchanged)
  - `GET /readyz` — readiness: can the service process jobs? (probes broker + S3)
- **Probe timeout**: 3 seconds max per dependency (configurable).
- **Response body**: JSON with per-dependency status so operators can see which one failed.
- **Non-blocking probes**: run S3 and broker probes concurrently.

## Implementation Plan

### Files to Change / Create

| File | Action | Notes |
|------|--------|-------|
| `http.go` | **MODIFY** | Add `GET /readyz` handler |
| `broker.go` | **MODIFY** | Add `Ping(ctx) error` method |
| `storage.go` | **MODIFY** | Add `Ping(ctx) error` to `ObjectStore` interface |

### New Dependencies

None.

### Interface / API Contract

```
GET /readyz
200 OK   → {"status":"ok","broker":"ok","storage":"ok"}
503 Service Unavailable → {"status":"degraded","broker":"ok","storage":"error: ..."}
```

Broker ping: `ch.ExchangeDeclarePassive("amq.direct", "direct", ...)` or `conn.IsClosed()`.
S3 ping: `HeadBucket` with 3s timeout.

## Implementation Notes

- `ObjectStore` interface gets a new method: `Ping(ctx context.Context) error`
- `s3Store.Ping` calls `HeadBucket` — zero-cost if bucket exists.
- `broker.Ping` checks `b.conn.IsClosed()` and optionally opens/closes a temp channel.
- Run probes with `errgroup` (or simple goroutines + channels) concurrently.

## Testing

- Unit test: mock broker Ping returning error → expect 503 response body with `"broker":"error: ..."`.
- Unit test: both ok → expect 200.

---

> [!summary] Change Summary
> Added `GET /readyz` to `http.go`. `ObjectStore` interface extended with `Ping(ctx) error` implemented via `HeadBucket` in `s3Store`. `broker.Ping()` checks `conn.IsClosed()`. Both probes run concurrently via goroutines with a 3-second timeout. Response: `{status, broker, storage}` JSON; 200 if all ok, 503 if any fails.

**Tags:** 
**Commit:** 
**PR / Branch:** 
