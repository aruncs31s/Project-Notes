---
created: 2026-07-28
tags:
  - goexport
  - docs
  - api
  - saas
  - sdk
status: draft
type: api-doc
---

# 06 SaaS Client SDK

## Installation

```bash
go get github.com/aruncs31s/goexport/client
```

## Quick Start

Initialize the client like a standard Go database/service driver (similar to AWS S3, Redis, or GORM):

```go
package main

import (
	"context"
	"fmt"
	"os"

	"github.com/aruncs31s/goexport/client"
)

func main() {
	// Initialize client driver
	c := client.New("http://localhost:8080", "my-secret-token",
		client.WithTenant("inst-101"),
		client.WithUser("user-42"),
	)

	// High-level single-line PDF generation: Submits, polls, and returns PDF bytes!
	pdfBytes, err := c.ExportURL(context.Background(), "https://example.com/invoice/42", "invoices")
	if err != nil {
		panic(err)
	}

	os.WriteFile("invoice.pdf", pdfBytes, 0644)
	fmt.Println("PDF saved to invoice.pdf")
}
```

## API Options & Call Overrides

Per-call overrides can be passed to any SDK method using call options:

```go
// Override tenant/user context for a single request
resp, err := c.CreateExport(ctx, client.ExportRequest{
	URL:     "https://example.com/report",
	Section: "academics",
}, client.WithTenant("inst-999"), client.WithUser("user-100"))
```

## Low-Level SDK Methods

| Method | Description |
|--------|-------------|
| `CreateExport(ctx, req, opts...)` | Submits an export job, returns `*ExportResponse` |
| `GetStatus(ctx, id, opts...)` | Checks job status, returns `*ExportStatus` |
| `DownloadPDF(ctx, id, opts...)` | Downloads PDF bytes for a completed export |
| `ListMyExports(ctx, section, limit, offset, opts...)` | Fetches tenant/user scoped exports |
| `ExportURL(ctx, url, section, opts...)` | High-level convenience helper: submits, polls, downloads PDF |

---

> [!summary] Change Summary
> Documented Go Client SDK initialization, functional options, low-level methods, and high-level `ExportURL` helper.

**Tags:** 
**Commit:** 
**PR / Branch:** 
