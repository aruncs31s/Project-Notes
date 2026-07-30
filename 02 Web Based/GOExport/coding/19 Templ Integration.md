---
tags:
  - templ
Status:
tested:
---
![[19 Templ Integration.png]]


# 19 Templ Integration

> [!summary] Change Summary
> **Feature**: Server-side rendering (SSR) of the admin website using Go `github.com/a-h/templ`.
> **Status**: ✅ Implemented — verification tests pass. Upgraded sidebar and overview page stat cards to use modern inline SVG icons (with animated spin transitions for processing gear).
> Tags:
> Commit:

## Overview

Migrates the admin dashboard from a raw embedded HTML string SPA to a compiled Go component template engine (`templ`). This provides type safety, modular component composition, and efficient server-side rendering.

## Architecture

We organize the templates inside `cmd/admin/templates/`:
- `layout.templ` — Shared layout containing navigation and global styling.
- `login.templ` — Standalone login screen.
- `overview.templ` — Main metrics dashboard and recent logs.
- `exports.templ` — Exports filter table with pagination.
- `tokens.templ` — Active client JWT tokens list and generator.
- `cleanup.templ` — Database maintenance commands.

## Build Step

Before compiling the Go binary, `.templ` files must be parsed to generate Go files:
```bash
go run github.com/a-h/templ/cmd/templ@latest generate
```

This generates `*_templ.go` sibling files next to the templates.

## Integration with Gin

We use a simple helper method to stream compiled templates directly to Gin's response writer:
```go
func renderTempl(c *gin.Context, status int, cmp templ.Component) {
    c.Status(status)
    c.Header("Content-Type", "text/html; charset=utf-8")
    _ = cmp.Render(c.Request.Context(), c.Writer)
}
```
