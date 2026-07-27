---
created: 2026-07-28
tags:
  - goexport
  - coding
  - rabbitmq
  - reliability
status: draft
feature: RabbitMQ Reconnect
type: coding-note
---

# 02 RabbitMQ Reconnect

## Overview

> [!abstract] TL;DR
> Wrap the `ConsumeSequentially` loop with exponential-backoff reconnection so the worker recovers automatically if the RabbitMQ connection drops.

## Why / Motivation

Currently, if RabbitMQ disconnects (network blip, broker restart), `ConsumeSequentially` returns an error and the goroutine in `main.go` exits permanently — but the HTTP server keeps running. The service appears healthy from the outside but silently stops processing all jobs. The only recovery is a full service restart.

## Design Decisions

- **Reconnect inside `broker`**: The broker wraps reconnect logic transparently. Callers (`ConsumeSequentially`) don't change.
- **Exponential backoff with cap**: Start at 1s, double each attempt, cap at 30s. Use `time.Sleep` with jitter (optional).
- **Re-declare queue on reconnect**: The durable queue must be re-declared after each reconnection to be safe.
- **Context cancellation respected**: If `ctx` is cancelled (graceful shutdown), the retry loop exits cleanly.
- **Log each reconnect attempt**: Structured log with attempt number and error.

## Implementation Plan

### Files to Change / Create

| File | Action | Notes |
|------|--------|-------|
| `broker.go` | **MODIFY** | Add `reconnect()` method, wrap `deliveries()` |
| `export.go` | **MODIFY** | `ConsumeSequentially` to handle `deliveries` channel close and retry |

### New Dependencies

None.

### Interface / API Contract

The `jobConsumer` interface stays the same. Reconnect is internal to broker.

```go
// broker internal
func (b *broker) reconnect(ctx context.Context) error
```

## Implementation Notes

- Use `amqp091.Connection.IsClosed()` to detect dead connection.
- On delivery channel close (`!ok` case in `ConsumeSequentially`), signal reconnect needed.
- Backoff: `min(1<<uint(attempt), 30)` seconds.
- After reconnect, call `deliveries()` again and resume the consume loop.

## Testing

- Unit test: inject a fake consumer that closes its channel after N deliveries; verify the outer loop calls `deliveries()` again.
- Manual: start service, kill RabbitMQ container, wait 10s, restart RabbitMQ — worker should recover.

---

> [!summary] Change Summary
> Implemented exponential-backoff reconnect loop inside `ConsumeSequentially` in `export.go`. The delivery loop is split into `consumeLoop` (runs while connected) and an outer loop that re-calls `deliveries()` on channel close. Backoff: 1s → doubles → capped at 30s. `broker.Ping()` added for the readiness probe.

**Tags:** 
**Commit:** 
**PR / Branch:** 
