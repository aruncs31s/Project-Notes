# ETLAB AuthZ Framework - UI Architecture Guide

## Overview

The ETLAB Authorization Framework provides a comprehensive UI system built on **Domain-Driven Design (DDD)** principles with full template inheritance and reusable components. This ensures consistent styling, maintainability, and scalability across all web pages.

## Architecture Principles

### 1. Template Inheritance
All pages inherit from a common base layout (`base.templ`), ensuring:
- Consistent HTML structure
- Common CSS/JS dependencies
- Unified theming and styling
- Dark mode support across all pages

### 2. Component-Based Design
Reusable UI components (`components.templ`) follow these principles:
- Single Responsibility: Each component does one thing well
- Composability: Components can be combined to build complex UIs
- Consistency: Same component looks/behaves the same everywhere
- Maintainability: Change once, apply everywhere

### 3. DDD Layering
```
Application Layer (UI Templates)
├── base.templ - Base layouts
├── components.templ - Reusable components
├── home.templ - Page-specific templates
├── login.templ
└── ...

Domain Layer (Data Structures)
├── HomePageData
├── BaseLayoutData
└── ...

Infrastructure Layer (Rendering)
└── Templ engine generates Go code
```

## Base Layout System

### BaseLayout
The root layout providing core HTML structure:
```go
@BaseLayout(BaseLayoutData{
    Title: "Page Title",
    Description: "Page description for SEO",
    CurrentPage: "pagename",
}) {
    <!-- Your content here -->
}
```

### BaseLayoutWithHeader
Adds a standard header with navigation:
```go
@BaseLayoutWithHeader(data, "username") {
    <!-- Your content here -->
}
```

### BaseLayoutWithSidebar
Provides sidebar navigation for admin pages:
```go
@BaseLayoutWithSidebar(data, "username") {
    <!-- Your content here -->
}
```

## Reusable Components

### Data Display Components

#### StatCard
Display statistical information:
```go
@StatCard(
    "Total Routes",                              // Label
    "1,234",                                      // Value
    "fas fa-route",                              // Icon class
    "bg-blue-100 dark:bg-blue-900/30",          // Icon background
    "text-blue-600 dark:text-blue-400",         // Icon color
    "Configured endpoints"                       // Subtitle
)
```

#### Card
Generic card container:
```go
@Card("Card Title", "fas fa-info", "bg-blue-100") {
    <!-- Card content -->
}
```

#### FeatureCard
Feature showcase with link:
```go
@FeatureCard(
    "API Analytics",                             // Title
    "Track usage and performance metrics",       // Description
    "fas fa-chart-bar",                         // Icon
    "bg-blue-100 dark:bg-blue-900/30",         // Icon background
    "text-blue-600 dark:text-blue-400",        // Icon color
    "/admin-ui/api_analytics",                  // Link URL
    "View Analytics"                             // Link text
)
```

### Interactive Components

#### Button
Consistent button styling:
```go
@Button(
    "Click Me",                                  // Text
    "primary",                                   // Type (primary/secondary/danger)
    "fas fa-arrow-right",                       // Icon class (optional)
    "/some/url"                                  // Link URL (optional)
)
```

#### FormInput
Form input with label:
```go
@FormInput(
    "username",                                  // ID
    "username",                                  // Name
    "text",                                      // Type
    "Username",                                  // Label
    "Enter your username",                       // Placeholder
    true                                         // Required
)
```

#### Alert
Display messages:
```go
@Alert("Operation successful!", "success")      // Types: error, success, warning, info
```

#### Badge
Labels and tags:
```go
@Badge("Active", "success")                     // Types: primary, success, danger, warning, default
```

### Layout Components

#### Table
Data table structure:
```go
@Table([]string{"Column 1", "Column 2", "Column 3"}) {
    <!-- Table rows -->
    <tr>
        <td>Data 1</td>
        <td>Data 2</td>
        <td>Data 3</td>
    </tr>
}
```

#### PageHeader
Consistent page headers:
```go
@PageHeader(
    "Dashboard",                                 // Title
    "Manage your authorization system",          // Description
    "fas fa-home",                              // Icon
    "bg-blue-100 dark:bg-blue-900/30"          // Icon background
)
```

#### Footer
Standard footer (automatically included in base layouts):
```go
@Footer()
```

### State Components

#### LoadingSpinner
Display loading state:
```go
@LoadingSpinner("medium")                       // Sizes: small, medium, large
```

#### EmptyState
Show when there's no data:
```go
@EmptyState(
    "fas fa-inbox",                             // Icon
    "No data found",                            // Title
    "Try adjusting your filters",               // Description
    "Add New Item",                             // Action button text
    "/add-item"                                 // Action button link
)
```

## Styling System

### CSS Variables
The framework defines CSS variables for consistent theming:

```css
:root {
    --primary-color: #2563eb;
    --primary-hover: #1d4ed8;
    --secondary-color: #7c3aed;
    --success-color: #10b981;
    --danger-color: #ef4444;
    --warning-color: #f59e0b;
    --info-color: #3b82f6;
    --light-bg: #f9fafb;
    --dark-bg: #111827;
    --border-radius: 0.5rem;
    --transition-speed: 200ms;
}
```

### Utility Classes

#### Framework Classes
- `.framework-card` - Card styling
- `.framework-btn` - Button base
- `.framework-btn-primary` - Primary button
- `.framework-input` - Input fields

#### Animation Classes
- `.fade-in` - Fade in animation
- `.slide-up` - Slide up animation

### Dark Mode
All components support dark mode through Tailwind's `dark:` prefix:
```html
<div class="bg-white dark:bg-gray-900 text-gray-900 dark:text-gray-100">
```

## Creating New Pages

### Step 1: Define Data Structure
```go
type MyPageData struct {
    Title   string
    Content string
}
```

### Step 2: Create Template
```go
templ MyPage(data MyPageData) {
    @BaseLayoutWithSidebar(BaseLayoutData{
        Title: "My Page",
        Description: "My page description",
        CurrentPage: "mypage",
    }, "admin") {
        <main class="flex-1 p-6">
            @PageHeader(
                "My Page Title",
                "Page description",
                "fas fa-star",
                "bg-blue-100 dark:bg-blue-900/30"
            )
            
            <!-- Use components -->
            @Card("Content", "", "") {
                <p>{ data.Content }</p>
            }
            
            @Footer()
        </main>
    }
}
```

### Step 3: Generate Go Code
```bash
cd application/templates
templ generate
```

### Step 4: Use in Handler
```go
func MyPageHandler(c *gin.Context) {
    data := templates.MyPageData{
        Title:   "Welcome",
        Content: "Hello World",
    }
    templates.MyPage(data).Render(c.Request.Context(), c.Writer)
}
```

## Best Practices

### 1. Always Use Base Layouts
Never create standalone HTML pages. Always extend from a base layout:
```go
// ✅ Good
@BaseLayoutWithSidebar(data, username) { ... }

// ❌ Bad
<!DOCTYPE html><html>...</html>
```

### 2. Use Components for Consistency
Prefer components over custom HTML:
```go
// ✅ Good
@StatCard("Total", "100", "fas fa-check", "bg-blue-100", "text-blue-600", "items")

// ❌ Bad
<div class="bg-white p-6 rounded">
    <p>Total: 100</p>
</div>
```

### 3. Follow DDD Principles
- Keep templates in Application Layer
- Define data structures in Domain Layer
- Handlers in Application Layer
- Keep business logic out of templates

### 4. Maintain Accessibility
- Use semantic HTML
- Include ARIA labels when needed
- Ensure keyboard navigation works
- Test with screen readers

### 5. Theme Compatibility
Always include dark mode variants:
```html
<div class="text-gray-900 dark:text-gray-100">
```

### 6. Performance
- Use icons from CDN (Font Awesome)
- Minimize custom CSS
- Leverage Tailwind's utility classes
- Keep JavaScript minimal (Alpine.js only)

## Migration Guide

### Converting Existing Templates

1. **Identify the layout type needed:**
   - Simple page: `BaseLayout`
   - With header: `BaseLayoutWithHeader`
   - Admin page: `BaseLayoutWithSidebar`

2. **Replace HTML boilerplate:**
   ```go
   // Before
   <!DOCTYPE html>
   <html>
   <head>...</head>
   <body>...</body>
   </html>
   
   // After
   @BaseLayout(data) {
       <!-- content -->
   }
   ```

3. **Replace repeated elements with components:**
   ```go
   // Before
   <div class="bg-white p-6 rounded shadow">
       <h3>Title</h3>
       ...
   </div>
   
   // After
   @Card("Title", "", "") {
       ...
   }
   ```

4. **Regenerate templates:**
   ```bash
   templ generate
   ```

## Troubleshooting

### Templates Not Updating
```bash
cd application/templates
templ generate
go build
```

### Styling Issues
1. Check dark mode classes are present
2. Verify Tailwind classes are correct
3. Clear browser cache

### Component Not Found
1. Ensure `components.templ` is generated
2. Check import statements
3. Rebuild project

## Examples

See these files for complete examples:
- `application/templates/home_with_sidebar.templ` - Sidebar layout with components
- `application/templates/base.templ` - Base layout definitions
- `application/templates/components.templ` - All component definitions

## Extending the Framework

### Adding New Components

1. Add component to `components.templ`:
```go
templ MyNewComponent(param string) {
    <div class="framework-card">
        { param }
    </div>
}
```

2. Regenerate:
```bash
templ generate
```

3. Use in templates:
```go
@MyNewComponent("Hello")
```

### Adding New Base Layouts

1. Add to `base.templ`:
```go
templ MyCustomLayout(data BaseLayoutData) {
    @BaseLayout(data) {
        <!-- custom structure -->
        { children... }
    }
}
```

2. Use in pages:
```go
@MyCustomLayout(data) {
    <!-- page content -->
}
```

## Conclusion

This UI architecture provides:
- **Consistency** through inheritance
- **Maintainability** through components
- **Scalability** through DDD principles
- **Flexibility** through composition

Follow these patterns to build a robust, maintainable web UI for the ETLAB Authorization Framework.
