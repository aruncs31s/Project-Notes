---
created: 2026-07-28
tags:
  - goexport
  - coding
  - devex
  - docker
status: draft
feature: Docker Compose Dev Setup
type: coding-note
---

# 12 Docker Compose Dev Setup

## Overview

> [!abstract] TL;DR
> Add a `docker-compose.yml` that spins up RabbitMQ and LocalStack (S3-compatible) so a new contributor can run the full stack locally with a single `docker compose up`.

## Why / Motivation

Onboarding currently requires:
1. Manually installing and running RabbitMQ
2. Configuring real AWS credentials + S3 bucket
3. Hand-editing `.env`

This creates significant friction. Docker Compose eliminates all three steps.

## Design Decisions

- **LocalStack** for S3: `localstack/localstack` image, enables only the `s3` service to keep it lightweight.
- **RabbitMQ management UI**: use `rabbitmq:4-management` so queue can be inspected at `localhost:15672`.
- **Init script**: a shell script (`scripts/init-localstack.sh`) that creates the S3 bucket on LocalStack startup.
- **`.env.docker`**: a pre-filled env file that points at the compose services — copy to `.env` for local dev.
- **App service optional**: include an `app` service definition (commented out) for full containerised runs.

## Implementation Plan

### Files to Change / Create

| File | Action | Notes |
|------|--------|-------|
| `docker-compose.yml` | **CREATE** | RabbitMQ + LocalStack services |
| `scripts/init-localstack.sh` | **CREATE** | `awslocal s3 mb s3://goexport-dev` |
| `.env.docker` | **CREATE** | Pre-filled env for compose |
| `Dockerfile` | **CREATE** | Multi-stage build for the app |
| `README.md` | **MODIFY** | Add "Local dev with Docker Compose" section |

### New Dependencies

None (tooling only).

### Interface / API Contract

```sh
# Bring up deps only
docker compose up -d rabbitmq localstack

# Full stack
docker compose up
```

## Implementation Notes

- LocalStack bucket init via `command` override or a `depends_on` healthcheck.
- RabbitMQ healthcheck: `rabbitmq-diagnostics -q check_port_connectivity`.
- LocalStack healthcheck: `curl -s http://localhost:4566/_localstack/health | grep '"s3": "available"'`.
- `.env.docker`:
  ```
  RABBITMQ_URL=amqp://guest:guest@localhost:5672/
  S3_BUCKET=goexport-dev
  AWS_REGION=us-east-1
  S3_ENDPOINT=http://localhost:4566
  AWS_ACCESS_KEY_ID=test
  AWS_SECRET_ACCESS_KEY=test
  ```

## Testing

```sh
docker compose up -d rabbitmq localstack
cp .env.docker .env
go run .
curl -X POST http://localhost:8080/exports -H 'Content-Type: application/json' -d '{"url":"https://example.com"}'
```

---

> [!summary] Change Summary
> _Fill in after implementation._

**Tags:** 
**Commit:** 
**PR / Branch:** 
