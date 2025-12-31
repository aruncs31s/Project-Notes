# Setting Up the Documentation Renderer

## Quick Start

The markdown documentation renderer has been fully implemented for your project. Follow these steps to activate it.

## What Was Created

### 1. Backend Handler
**File**: `application/handler/docs_handler.go`

The handler manages:
- Reading markdown files from the docs directory
- Building folder structure for sidebar navigation
- Extracting document titles
- Security validation (no path traversal)

**Key Functions**:
- `NewDocsHandler(docsPath string)` - Initialize handler
- `GetDocsPage(c *gin.Context)` - Render docs page
- `GetDocsList(c *gin.Context)` - Return docs structure as JSON

### 2. Frontend Template
**File**: `application/templates/docs_viewer.templ`

The Templ template provides:
- Beautiful responsive layout with sidebar
- Automatic markdown rendering using marked.js
- Syntax highlighting with highlight.js
- XSS protection with DOMPurify
- Mobile-friendly design

### 3. Route Setup
**File**: `application/routes/docs_routes.go`

Helper functions for easy route registration:
- `SetupDocsRoutes()` - Basic setup
- `SetupDocsRoutesWithMiddleware()` - With auth/logging
- `SetupDocsRoutesWithPrefix()` - Custom URL path

## Integration Steps

### Step 1: Generate Templ Files

Compile the Templ template:

```bash
templ generate
```

This creates `docs_viewer_templ.go` automatically.

### Step 2: Add Routes to Your Application

In your main application setup file (e.g., `setup.go` or `main.go`), add:

```go
import (
    "github.com/aruncs31s/etlabauthzframework/application/routes"
)

// In your router setup function:
func setupRoutes(router *gin.Engine) {
    // Your existing routes...
    
    // Add documentation routes
    routes.SetupDocsRoutes(router, "docs")
}
```

### Step 3: Create Docs Folder Structure

Create your documentation in the `docs/` folder:

```
docs/
├── README.md                 # Main docs page
├── QUICKSTART.md
├── USER_GUIDE.md
├── API/
│   ├── README.md
│   ├── Endpoints.md
│   └── Authentication.md
└── ARCHITECTURE/
    ├── Overview.md
    └── Components.md
```

### Step 4: Start Your Application

```bash
go run .
```

Navigate to: `http://localhost:PORT/docs`

## Usage

### View Documentation
- **Main page**: `http://localhost:8080/docs`
- **Specific doc**: `http://localhost:8080/docs?path=API/Endpoints`
- **Nested doc**: `http://localhost:8080/docs?path=ARCHITECTURE/Overview`

### API Endpoint
Get documentation structure as JSON:
```
GET /api/docs/list
```

Response:
```json
{
  "docs": [
    {
      "name": "README",
      "path": "README",
      "is_folder": false
    },
    {
      "name": "API",
      "path": "API",
      "is_folder": true,
      "children": [...]
    }
  ]
}
```

## Features

✅ **Folder Structure Navigation** - Sidebar follows your docs folder layout  
✅ **Markdown Support** - Full GitHub-flavored markdown support  
✅ **Code Highlighting** - Automatic syntax highlighting for code blocks  
✅ **XSS Protection** - HTML sanitization for security  
✅ **Responsive Design** - Works on desktop, tablet, and mobile  
✅ **Fast Loading** - Client-side rendering (no server overhead)  
✅ **Path Security** - No directory traversal vulnerabilities  

## Advanced Configuration

### With Authentication

```go
// Create auth middleware
func requireAuth() gin.HandlerFunc {
    return func(c *gin.Context) {
        token := c.GetHeader("Authorization")
        if token == "" {
            c.JSON(http.StatusUnauthorized, gin.H{"error": "unauthorized"})
            c.Abort()
            return
        }
        c.Next()
    }
}

// Use with docs routes
routes.SetupDocsRoutesWithMiddleware(
    router,
    "docs",
    requireAuth(),
)
```

### Custom URL Path

```go
// Docs available at /help instead of /docs
routes.SetupDocsRoutesWithPrefix(router, "docs", "/help")

// Now accessible at:
// GET /help
// GET /api/help/list
```

### Different Docs Directory

```go
// Use custom docs path
routes.SetupDocsRoutes(router, "path/to/documentation")
```

## Customization

### Styling

Edit CSS in `docs_viewer.templ` (around line 15-350):

```css
/* Change sidebar width */
.sidebar {
    width: 280px;  /* Adjust this */
}

/* Change primary color */
.tree-link.active {
    color: #0066cc;  /* Change this */
}
```

### Markdown Options

Modify marked.js configuration in the template (around line 390):

```javascript
marked.setOptions({
    breaks: true,    // Enable line breaks
    gfm: true,       // GitHub-flavored markdown
    headerIds: true, // Generate header IDs
});
```

## Supported Markdown Features

- ✅ Headers (H1-H6)
- ✅ Bold, italic, strikethrough
- ✅ Code blocks with syntax highlighting
- ✅ Lists (ordered and unordered)
- ✅ Tables
- ✅ Blockquotes
- ✅ Links and images
- ✅ Horizontal rules
- ✅ Line breaks

## Security

- **Path Validation**: Prevents directory traversal with `..` checks
- **XSS Protection**: All HTML is sanitized before rendering
- **File Filtering**: Only `.md` files are served
- **Access Control**: Can be restricted with middleware

## Troubleshooting

### Docs Not Appearing

1. Check `docs/` folder exists in project root
2. Verify files have `.md` extension
3. Run `templ generate` to compile templates
4. Restart the application

### Styles Look Wrong

1. Clear browser cache (Ctrl+Shift+Del or Cmd+Shift+Del)
2. Check that CSS loads in browser DevTools
3. Verify template compiled correctly

### Markdown Not Rendering

1. Check markdown syntax is valid
2. Ensure files are UTF-8 encoded
3. Look for JavaScript errors in browser console
4. Verify CDN scripts loaded (marked.js, DOMPurify)

### Can't Navigate to Docs

1. Verify routes are registered in your app
2. Check URL matches configured path
3. Confirm application started successfully
4. Check browser console for errors

## File Organization Best Practices

```
docs/
├── README.md              # Start here, general overview
├── GETTING_STARTED.md     # Installation and setup
├── guides/
│   ├── README.md
│   ├── authentication.md
│   ├── authorization.md
│   └── api-usage.md
├── api/
│   ├── README.md
│   ├── endpoints.md
│   ├── responses.md
│   └── errors.md
├── examples/
│   ├── basic-setup.md
│   ├── advanced-usage.md
│   └── common-patterns.md
└── architecture/
    ├── overview.md
    ├── components.md
    └── database.md
```

## Performance Tips

- Keep markdown files reasonably sized (< 10MB)
- Use clear headings for better navigation
- Add code examples to guides
- Link between related documents
- Update docs regularly

## Next Steps

1. ✅ Create your documentation in `/docs` folder
2. ✅ Run `templ generate` to compile templates
3. ✅ Add route setup to your application
4. ✅ Start the server
5. ✅ Visit `/docs` in your browser

## Integration Checklist

- [ ] Templ templates generated (`templ generate`)
- [ ] Routes added to application setup
- [ ] `/docs` folder created
- [ ] Markdown files added to `/docs`
- [ ] Application tested and working
- [ ] Documentation accessible at `/docs`

## Support Files

- **Implementation Guide**: `DOCS_RENDERER_GUIDE.md`
- **Handler**: `application/handler/docs_handler.go`
- **Template**: `application/templates/docs_viewer.templ`
- **Routes**: `application/routes/docs_routes.go`

## Example Implementation

```go
package main

import (
    "github.com/aruncs31s/etlabauthzframework/application/routes"
    "github.com/gin-gonic/gin"
)

func main() {
    router := gin.Default()
    
    // Load templates (important!)
    router.LoadHTMLGlob("application/templates/*.html")
    
    // Setup documentation routes
    routes.SetupDocsRoutes(router, "docs")
    
    // Start server
    router.Run(":8080")
}
```

## External Libraries (CDN)

The renderer uses these libraries via CDN (no npm install needed):

- **marked.js** v11.1.1 - Markdown parser
- **DOMPurify** v3.0.6 - HTML sanitizer
- **highlight.js** v11.9.0 - Syntax highlighter

All are loaded from JSDelivr CDN.

## Version Info

- **Created**: 2024
- **Go Version**: 1.25.3+
- **Templ Version**: 0.3.960+
- **Gin Version**: 1.11.0+

---

Your documentation renderer is ready to use! Start by creating your markdown files in the `/docs` folder, then visit `/docs` in your browser. 📚