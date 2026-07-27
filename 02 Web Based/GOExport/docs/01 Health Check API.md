---
created: 2026-07-28
tags:
  - goexport
  - docs
  - api
  - health
status: draft
type: api-doc
---

# 01 Health Check API

## Endpoints

### `GET /healthz` — Liveness

Always returns `204 No Content`. Used by the container runtime to know if the process is alive.

```http
GET /healthz HTTP/1.1
```

```http
HTTP/1.1 204 No Content
```

---

### `GET /readyz` — Readiness

Probes RabbitMQ broker connectivity and S3 bucket accessibility. Used by load balancers to decide whether to route traffic.

**Request**
```http
GET /readyz HTTP/1.1
```

**Response — Healthy**
```http
HTTP/1.1 200 OK
Content-Type: application/json

{
  "status": "ok",
  "broker": "ok",
  "storage": "ok"
}
```

**Response — Degraded**
```http
HTTP/1.1 503 Service Unavailable
Content-Type: application/json

{
  "status": "degraded",
  "broker": "ok",
  "storage": "error: failed to head bucket: ..."
}
```

## Notes

- Both probes run concurrently with a 3-second timeout each.
- A 503 on `/readyz` will cause k8s / ECS to stop sending new traffic to this pod/task.
- `/healthz` returning 204 does **not** mean the service can process jobs — use `/readyz` for that.

---

> [!summary] Change Summary
> Added `GET /readyz` endpoint. `/healthz` is unchanged (liveness only).

**Tags:** 
**Commit:** 
**PR / Branch:** 
