---
created: 2026-07-28
tags:
  - goexport
  - coding
  - s3
  - presigned
status: draft
feature: S3 Pre-Signed URL
type: coding-note
---

# 09 S3 Pre-Signed URL

## Overview

> [!abstract] TL;DR
> Change `GET /exports/:id/pdf` to return a 302 redirect to a pre-signed S3 URL instead of streaming the PDF bytes through the server.

## Why / Motivation

Currently the server fetches the entire PDF from S3 into memory and streams it to the client. For a 10MB PDF with 50 concurrent downloads, the server is holding 500MB in RAM and consuming significant outbound bandwidth. A pre-signed URL offloads the transfer entirely to S3/CloudFront.

## Design Decisions

- **302 Redirect**: simplest client-compatible approach. Browsers and `curl -L` follow it automatically.
- **Pre-sign TTL**: 15 minutes (configurable via `PRESIGN_TTL` env var). Short enough to be secure, long enough to survive a slow client.
- **Fallback**: if `S3_ENDPOINT` is set (local/dev mode), stream bytes directly because LocalStack pre-signed URLs may not be accessible from outside the container network.
- **New `ObjectStore` method**: `PresignURL(ctx, key, ttl) (string, error)`.

## Implementation Plan

### Files to Change / Create

| File | Action | Notes |
|------|--------|-------|
| `storage.go` | **MODIFY** | Add `PresignURL` to `ObjectStore` interface + `s3Store` impl |
| `http.go` | **MODIFY** | `GET /exports/:id/pdf` → redirect to pre-signed URL |
| `internal/config/initializers.go` | **MODIFY** | Add `PresignTTL time.Duration`, `S3Endpoint` already exists |

### New Dependencies

```
go get github.com/aws/aws-sdk-go-v2/service/s3/s3presign  // already transitive
```

Actually uses `s3.NewPresignClient` — no new dependency.

### Interface / API Contract

```go
// ObjectStore
PresignURL(ctx context.Context, key string, ttl time.Duration) (url string, err error)

// HTTP
GET /exports/:id/pdf
→ 302 Location: https://bucket.s3.amazonaws.com/exports/section/id.pdf?X-Amz-Signature=...
```

## Implementation Notes

- `s3.NewPresignClient(s.client)` then `presignClient.PresignGetObject(ctx, &s3.GetObjectInput{...}, s3.WithPresignExpires(ttl))`
- Fallback detection: `if c.S3Endpoint != "" { stream bytes } else { redirect }`
- Test fake: `fakeStore.PresignURL` returns `"https://fake.s3/key?sig=test"`.

## Testing

- Unit test: mock `PresignURL` returning a URL; verify response is 302 with correct `Location` header.
- Unit test: fallback path (S3Endpoint set) returns 200 with PDF bytes.

---

> [!summary] Change Summary
> _Fill in after implementation._

**Tags:** 
**Commit:** 
**PR / Branch:** 
