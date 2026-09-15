# 21 Raw HTML PDF Rendering

> [!summary] Change Summary
> **Feature**: Added support for generating PDFs from raw HTML payloads directly (mutually exclusive with URLs). Includes Go Client SDK integration, interactive console tester updates, and details modal rendering optimizations.
> **Status**: ✅ Implemented — verification tests pass
> Tags:
> Commit:

## Overview

Previously, the service only supported rendering PDFs by navigating Chrome to a target `URL`. We have added support for passing raw `HTML` strings directly. Chrome boots, loads an empty page, sets the document content directly to the provided HTML body via Chrome DevTools Protocol (`page.SetDocumentContent`), and prints it to a PDF.

## Proposed Changes

### Component: Core rendering
- `engines/chromedp.go`: Changed `NewChromeRenderer()` to return two functions: `renderURL` and `renderHTML`.
  - `renderHTML` navigates to `about:blank`, retrieves the root frame ID, and calls `page.SetDocumentContent(frameID, html)` before triggering `page.PrintToPDF`.

### Component: Models & Router
- `internal/models/export.go`: Added `HTML string` field to `ExportJob`.
- `internal/handlers/http/exports.go`: Accepts `html` in the JSON request body. Performs mutual exclusivity validation (either `url` or `html` must be provided, but not both).
- `internal/services/export/export.go`: Updates the export loop `process()` to direct HTML-based jobs to `s.RenderHTML` and URL-based jobs to `s.Render`.

### Component: Go Client SDK
- `pkg/client/client.go`:
  - Added `HTML string` field to `ExportRequest`.
  - Added high-level method `c.ExportHTML(ctx, html, section, pollInterval)` which submits raw HTML and returns the downloaded PDF bytes.
- `pkg/client/example/`:
  - `main.go`: Added `/sdk/export-html` endpoint.
  - `index.html`: Added a "Target HTML" textarea and a `client.ExportHTML()` button.
