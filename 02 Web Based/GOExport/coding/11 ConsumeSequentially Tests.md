---
created: 2026-07-28
tags:
  - goexport
  - coding
  - testing
  - coverage
status: draft
feature: ConsumeSequentially Tests
type: coding-note
---

# 11 ConsumeSequentially Tests

## Overview

> [!abstract] TL;DR
> Add unit tests for the `ConsumeSequentially` consume loop covering: successful ack, render failure → nack+requeue, bad JSON → reject, and context cancellation exit.

## Why / Motivation

`ConsumeSequentially` is the heart of the worker — it handles all job lifecycle transitions — yet it has zero test coverage. It's also one of the trickiest parts to get right (ack vs nack vs reject semantics).

## Design Decisions

- **Fake `jobConsumer`**: implement the `jobConsumer` interface with a `fakeConsumer` that returns a pre-populated `chan amqp091.Delivery`.
- **Fake `amqp091.Delivery`**: use `amqp091.Delivery` with an `Acknowledger` field — implement a `fakeAcknowledger` that records which methods were called.
- **Table-driven tests**: one table with scenarios: ok, render-fail, bad-json, ctx-cancel.

## Implementation Plan

### Files to Change / Create

| File | Action | Notes |
|------|--------|-------|
| `export_test.go` | **MODIFY** | Add `fakeConsumer`, `fakeAcknowledger`, test cases for `ConsumeSequentially` |

### New Dependencies

None.

### Interface / API Contract

```go
type fakeAcknowledger struct {
    acked    bool
    nacked   bool
    rejected bool
    requeue  bool
}

type fakeConsumer struct {
    ch chan amqp091.Delivery
}
func (f *fakeConsumer) deliveries() (<-chan amqp091.Delivery, *amqp091.Channel, error) { return f.ch, nil, nil }
```

## Implementation Notes

- `amqp091.Delivery.Acknowledger` is an interface — `fakeAcknowledger` implements `Ack`, `Nack`, `Reject`.
- Scenario table:
  | Scenario | Delivery | Expected |
  |----------|----------|----------|
  | OK render | valid job JSON, render returns pdf | Ack called |
  | Render fail | valid JSON, render returns err | Nack(requeue=true) called |
  | Bad JSON | `[]byte("not-json")` | Reject(requeue=false) called |
  | Ctx cancel | cancel before delivery | returns `context.Canceled` |

## Testing

These notes **are** the test plan.

---

> [!summary] Change Summary
> _Fill in after implementation._

**Tags:** 
**Commit:** 
**PR / Branch:** 
