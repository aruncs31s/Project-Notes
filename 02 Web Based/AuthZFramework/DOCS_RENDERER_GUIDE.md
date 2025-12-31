# Documentation Renderer Implementation Guide

## Overview

This guide explains how to integrate the markdown documentation renderer into your etlabauthzframework application. The renderer provides a beautiful, responsive interface for browsing markdown documentation with sidebar navigation following your folder structure.

## Features

- **Folder Structure Navigation**: Automatically mirrors your `/docs` folder structure in the sidebar
- **Markdown Rendering**: Uses `marked.js` to convert markdown to HTML
- **Syntax Highlighting**: Code blocks are highlighted with `highlight.js`
- **XSS Protection**: Content is sanitized with DOMPurify
- **Responsive Design**: Works on desktop and mobile devices
- **Clean UI**: Modern, professional interface with smooth interactions

## Files Created

### 1. Handler (`application/handler/docs_handler.go`)
Handles the core logic for:
- Reading markdown files from the docs directory
- Building folder tree structure for navigation
- Extracting titles from markdown content
- Security checks for path traversal attacks

**Key Methods:**
- `NewDocsHandler(docsPath string)` - Creates a new handler
- `GetDocsPage(c *gin.Context)` - Renders documentation page
- `GetDocsList(c *gin.Context)` - Returns docs structure as JSON
- `buildNavTree()` - Recursively builds folder structure
- `extractTitle()` - Extracts first heading from markdown

### 2. Template (`application/templates/docs_viewer.templ`)
Renders the complete HTML page with:
- Responsive sidebar with folder navigation
- Main content area with markdown rendering
- JavaScript integration for markdown parsing
- Syntax highlighting for code blocks

**Dependencies:**
- `marked.js` (v11.1.1) - Markdown parser
- `DOMPurify` (v3.0.6) - XSS sanitization
- `highlight.js` (v11.9.0) - Syntax highlighting

### 3. Routes (`application/routes/docs_routes.go`)
Sets up HTTP routes:
- `GET /docs` - Display documentation page
- `GET /api/docs/list` - Get documentation structure as JSON

## Installation Steps

### Step 1: Verify Handler and Templates

The handler and template files have already been created:
- ✅ `application/handler/docs_handler.go`
- ✅ `application/templates/docs_viewer.templ`
- ✅ `application/routes/docs_routes.go`

### Step 2: Generate Templ Files

Compile the Templ template to generate the Go code:

```bash
# If you have templ CLI installed
templ generate

# Or using Go
go generate ./application/templates/...
```

This will create `docs_viewer_templ.go` automatically.

### Step 3: Integrate Routes into Your Application

In your main application setup (typically in `etlabauthzframework.go` or your main setup file), add the docs routes:

```go
import (
    "github.com/aruncs31s/etlabauthzframework/application/routes"
    "github.com/gin-gonic/gin"
)

func setupRouter(router *gin.Engine) {
    // ... existing routes ...
    
    // Setup documentation routes
    docsPath := "docs" // Path to your docs folder
    routes.SetupDocsRoutes(router, docsPath)
}
```

### Step 4: Organize Your Documentation

Create markdown files in the `/docs` folder following this structure:

```
docs/
├── README.md
├── getting-started/
│   ├── README.md
│   ├── installation.md
│   └── quickstart.md
├── guides/
│   ├── authentication.md
│   ├── authorization.md
│   └── api-usage.md
├── api/
│   ├── endpoints.md
│   ├── authentication.md
│   └── errors.md
└── examples/
    ├── basic-setup.md
    └── advanced-usage.md
```

### Step 5: Start Your Application

```bash
go run .
```

Navigate to `http://localhost:PORT/docs` to view your documentation.

## Markdown Features Supported

The renderer supports all common markdown features:

### Headers
```markdown
# H1 Header
## H2 Header
### H3 Header
```

### Text Formatting
```markdown
**Bold text**
*Italic text*
~~Strikethrough~~
```

### Code
```markdown
Inline `code`

\`\`\`go
func main() {
    fmt.Println("Hello World")
}
\`\`\`
```

### Lists
```markdown
- Item 1
- Item 2
  - Nested item

1. First
2. Second
3. Third
```

### Links and Images
```markdown
[Link text](https://example.com)
![Alt text](image.png)
```

### Tables
```markdown
| Header 1 | Header 2 |
|----------|----------|
| Cell 1   | Cell 2   |
| Cell 3   | Cell 4   |
```

### Blockquotes
```markdown
> This is a blockquote
> Multiple lines work too
```

## Configuration

### Custom Docs Path

If your docs are in a different location:

```go
routes.SetupDocsRoutes(router, "path/to/your/docs")
```

### With Authentication Middleware

To protect the docs with authentication:

```go
routes.SetupDocsRoutesWithMiddleware(
    router,
    "docs",
    authMiddleware,
    loggingMiddleware,
)
```

## URL Structure

- `/docs` - View documentation
- `/docs?path=guides/authentication` - View specific document
- `/api/docs/list` - Get folder structure as JSON

## API Response Format

`GET /api/docs/list` returns:

```json
{
  "docs": [
    {
      "name": "README",
      "path": "README",
      "is_folder": false,
      "children": null
    },
    {
      "name": "guides",
      "path": "guides",
      "is_folder": true,
      "children": [
        {
          "name": "authentication",
          "path": "guides/authentication",
          "is_folder": false
        }
      ]
    }
  ]
}
```

## Customization

### Styling

To customize colors and styling, edit the CSS in `docs_viewer.templ`:

```css
:root {
    --primary-color: #0066cc;
    --background: #f5f5f5;
    --sidebar-bg: #ffffff;
}
```

### Markdown Rendering Options

Modify the `marked` configuration in `docs_viewer.templ`:

```javascript
marked.setOptions({
    breaks: true,           // Add line breaks
    gfm: true,             // Use GitHub Flavored Markdown
    headerIds: true,       // Generate header IDs
});
```

## Troubleshooting

### Docs Not Loading
- Check that the `docs` folder exists
- Verify markdown files have `.md` extension
- Check console for JavaScript errors
- Ensure CDN scripts loaded (marked.js, DOMPurify, highlight.js)

### Styles Not Applied
- Clear browser cache
- Check that CSS in template loads correctly
- Verify no conflicting CSS on the page

### Markdown Not Rendering
- Verify markdown syntax is correct
- Check browser console for JavaScript errors
- Ensure files are valid UTF-8 encoded

### Path Navigation Issues
- Check that folder structure is correct
- Verify file paths don't contain special characters
- Check browser console for security warnings

## Security Considerations

1. **Path Traversal Protection**: The handler validates all paths to prevent directory traversal attacks
2. **XSS Protection**: Content is sanitized with DOMPurify before rendering
3. **File Validation**: Only `.md` files are served
4. **Access Control**: Can be restricted with middleware

## Performance

- Sidebar navigation is cached after first load
- Code highlighting happens client-side (no server overhead)
- Markdown files are read on-demand
- Large documents load efficiently

## Browser Support

Works on all modern browsers:
- Chrome/Chromium 90+
- Firefox 88+
- Safari 14+
- Edge 90+

## Development

### Adding New Features

1. **Add Handler Method** (`docs_handler.go`)
   ```go
   func (h *DocsHandler) NewFeature(c *gin.Context) {
       // Implementation
   }
   ```

2. **Add Route** (`docs_routes.go`)
   ```go
   router.GET("/docs/feature", func(c *gin.Context) {
       docsHandler.NewFeature(c)
   })
   ```

3. **Update Template** (`docs_viewer.templ`)
   - Add new HTML elements
   - Regenerate with `templ generate`

### Testing

Test the documentation renderer:

```bash
# Navigate to docs
curl http://localhost:8080/docs

# Get docs list
curl http://localhost:8080/api/docs/list
```

## Integration with Existing UI

To add a link to documentation in your existing UI:

```html
<a href="/docs">Documentation</a>
<a href="/docs?path=guides/authentication">Auth Guide</a>
```

## Maintenance

- Keep markdown files up-to-date
- Monitor CDN availability for external scripts
- Test docs after major updates
- Backup documentation regularly

## Support

For issues or questions:
1. Check this guide first
2. Review browser console for errors
3. Verify file structure is correct
4. Check that all external CDN scripts load

## Next Steps

1. Create your markdown documentation
2. Organize in the `/docs` folder
3. Run `templ generate`
4. Integrate routes into your application
5. Navigate to `/docs` to view

Enjoy your new documentation renderer! 📚