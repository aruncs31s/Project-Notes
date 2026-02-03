# Home UI Dashboard - Usage & Testing Guide

## Quick Start

### 1. Access the Home Dashboard

After authenticating as an admin:

```
http://localhost:YOUR_PORT/admin-ui
```

### 2. What You'll See

The home dashboard displays:
- **Quick stats**: Routes, audit logs, requests, response time
- **Feature cards**: Links to main admin tools
- **System info**: Framework details and capabilities
- **Quick actions**: One-click access to key features

## Features Overview

### Dashboard Statistics

#### Total Routes
- **What it shows**: Number of configured routes in your application
- **Source**: Loaded from `enterprise_route_metadata.json`
- **Use case**: Quick view of API endpoint coverage

#### Audit Logs
- **What it shows**: Total authorization events logged
- **Source**: Retrieved from API usage analytics
- **Use case**: Monitor security event volume

#### Total Requests
- **What it shows**: Total API calls tracked by the system
- **Source**: Aggregated from API usage statistics
- **Use case**: Understand traffic patterns

#### Average Response Time
- **What it shows**: Mean response time across all endpoints
- **Source**: Calculated from API usage data
- **Unit**: Milliseconds (ms)
- **Use case**: Identify performance issues

### Management Tools

#### 🎨 API Analytics
- **URL**: `/admin-ui/api_analytics`
- **Features**:
  - Top endpoints by usage
  - Slowest endpoints
  - Most error-prone endpoints
  - Usage trends over time
  - Success/error rates

#### 🛣️ Route Metadata Management
- **URL**: `/admin-ui/route_metadata`
- **Features**:
  - View all routes
  - Edit route configurations
  - Add new routes
  - Delete routes
  - Export/import route configs
  - View API versions and deprecations

#### 🔐 RBAC Policies
- **Location**: `config/casbin_rbac_policy.csv`
- **Features**:
  - Define role-based access rules
  - Map roles to resources/actions
  - Environment-specific policies

#### ⚡ Rate Limiting
- **Configuration**: Programmatically in setup
- **Features**:
  - Role-specific rate limits
  - Burst allowance
  - Redis support for distributed systems

#### 📋 Audit Logs
- **Source**: Authorization audit trail
- **Tracks**:
  - Access attempts (allowed/denied)
  - User identity and roles
  - Resource and action
  - Timestamp and IP address
  - Rate limit events
  - Deprecated route access

#### 👤 Admin Authentication
- **Purpose**: Manage admin login credentials
- **Features**:
  - Admin username/password management
  - Session control
  - Logout functionality

## Testing the Home Page

### 1. Unit Testing

Test the GetHomePage handler:

```go
func TestGetHomePage(t *testing.T) {
    // Setup
    configProvider, _ := config.NewAdminConfigProvider()
    handler := handler.NewPerformanceHandler(configProvider)
    
    // Create test request
    req, _ := http.NewRequest("GET", "/admin-ui", nil)
    w := httptest.NewRecorder()
    
    // Create gin context
    c, _ := gin.CreateTestContext(w)
    c.Request = req
    c.Set("claims", jwt.MapClaims{"username": "testadmin"})
    
    // Execute
    handler.GetHomePage(c)
    
    // Assert
    assert.Equal(t, http.StatusOK, w.Code)
    assert.Contains(t, w.Body.String(), "ETLAB AuthZ")
    assert.Contains(t, w.Body.String(), "Dashboard")
}
```

### 2. Integration Testing

Test the route registration:

```go
func TestHomeRouteRegistered(t *testing.T) {
    r := gin.Default()
    
    // Setup routes
    etlabauthzframework.SetupUI(r)
    
    // Test home route exists and is protected
    req, _ := http.NewRequest("GET", "/admin-ui", nil)
    w := httptest.NewRecorder()
    r.ServeHTTP(w, req)
    
    // Should redirect to login if not authenticated
    assert.Equal(t, http.StatusUnauthorized, w.Code)
}
```

### 3. Manual Testing Checklist

- [ ] **Homepage Load**: Navigate to `/admin-ui` and page loads
- [ ] **Authentication**: Redirects to login if not authenticated
- [ ] **Statistics Display**: All 4 stat cards display correctly
- [ ] **Feature Cards**: All 6 feature cards are visible
- [ ] **Navigation Links**: Click each link and verify navigation
- [ ] **Dark Mode**: Toggle dark mode and verify styling
- [ ] **Responsive Design**: Test on mobile (375px), tablet (768px), desktop (1920px)
- [ ] **Performance**: Page loads within 2 seconds
- [ ] **Accessibility**: Navigate using keyboard only
- [ ] **Error Handling**: Check console for errors

### 4. Browser Testing

Test across browsers:

```
Chrome/Edge (Latest)
├── Desktop view ✓
├── Mobile view ✓
└── Dark mode ✓

Firefox (Latest)
├── Desktop view ✓
├── Mobile view ✓
└── Dark mode ✓

Safari (Latest)
├── Desktop view ✓
├── Mobile view ✓
└── Dark mode ✓
```

### 5. Performance Testing

Test load time and responsiveness:

```bash
# Using curl
curl -w "@curl-format.txt" -o /dev/null -s http://localhost:8080/admin-ui

# Using Apache Bench
ab -n 100 -c 10 http://localhost:8080/admin-ui

# Using wrk
wrk -t4 -c100 -d30s http://localhost:8080/admin-ui
```

Expected metrics:
- **Page Load Time**: < 2000ms
- **Time to First Byte**: < 200ms
- **DOM Content Loaded**: < 1000ms
- **Fully Loaded**: < 2000ms

## API Integration

### Data Endpoints

The home page fetches data from these internal services:

```go
// Get admin username
claims := c.Get("claims")
username := claims.(jwt.MapClaims)["username"]

// Get routes
routes, _ := routes.LoadEnterpriseRouteMetadata("")

// Get analytics
summary, _ := h.apiUsageAnalytics.GetUsageSummary()
```

### Error Handling

The page gracefully handles missing data:

```go
if usageSummary == nil {
    usageSummary = &service.UsageSummaryDTO{}
    // Displays zero values
}
```

## Customization Guide

### 1. Change Colors

Edit `home.templ` and update Tailwind classes:

```templ
<!-- Change from blue to purple -->
<div class="bg-purple-100 dark:bg-purple-900/30">
    <i class="fas fa-route text-purple-600 dark:text-purple-400"></i>
</div>
```

### 2. Add New Stats

Update `HomePageData` struct:

```go
type HomePageData struct {
    // ... existing fields
    UptimePercentage float64
    CriticalEvents   int
}
```

Add stat card to template:

```templ
<div class="bg-white dark:bg-gray-800 rounded-lg p-6">
    <p>Uptime: { fmt.Sprintf("%.2f%%", data.UptimePercentage) }</p>
</div>
```

### 3. Modify Feature Cards

Edit feature grid section in `home.templ`:

```templ
<a href="/admin-ui/new-feature">
    <div class="bg-white rounded-lg p-6">
        <h4>New Feature</h4>
        <p>Description</p>
    </div>
</a>
```

### 4. Update System Information

Modify the info panels at bottom of template:

```templ
<li class="flex items-start">
    <i class="fas fa-circle-dot mr-3"></i>
    <span>Your new capability</span>
</li>
```

## Troubleshooting

### Issue: Page Shows Blank

**Solution**: Check authentication middleware
```go
// Verify CheckAdminAuth() is configured
r.GET("/admin-ui", middleware.CheckAdminAuth(), handler.GetHomePage)
```

### Issue: Stats Show Zero

**Solution**: Ensure database is initialized
```go
// Check database connection
db.AutoMigrate(&api_usage.APIUsageLog{}, &api_usage.APIUsageStats{})
```

### Issue: Template Not Found

**Solution**: Verify templ generation
```bash
cd /path/to/etlabauthzframework
$HOME/go/bin/templ generate
```

### Issue: Dark Mode Not Working

**Solution**: Check dark mode script
```html
<!-- Verify DarkModeStyles() and DarkModeToggle() are included -->
@DarkModeStyles()
@DarkModeToggle()
```

### Issue: Links Not Working

**Solution**: Check route registration
```go
// Verify all routes are registered in SetupUI()
r.GET("/admin-ui/api_analytics", middleware.CheckAdminAuth(), handler.GetAPIAnalyticsPage)
r.GET("/admin-ui/route_metadata", middleware.CheckAdminAuth(), handler.GetRouteMetadataManagementPage)
```

## Performance Optimization

### 1. Cache Statistics

Implement caching for frequently accessed stats:

```go
var lastStatsCache *service.UsageSummaryDTO
var lastStatsCacheTime time.Time

func GetCachedStats() *service.UsageSummaryDTO {
    if time.Since(lastStatsCacheTime) < 30*time.Second {
        return lastStatsCache
    }
    // Fetch fresh data
    lastStatsCache, _ = service.GetUsageSummary()
    lastStatsCacheTime = time.Now()
    return lastStatsCache
}
```

### 2. Lazy Load Heavy Data

Use JavaScript to load data after page render:

```javascript
// Load audit logs asynchronously
fetch('/api/audit/summary')
    .then(r => r.json())
    .then(data => {
        document.getElementById('audit-count').textContent = data.count;
    });
```

### 3. Optimize Images/Icons

- Use SVG instead of PNG where possible
- Implement CDN caching for static resources
- Compress images before deployment

## Security Considerations

1. **Authentication**: Always protect `/admin-ui` routes
2. **Authorization**: Verify admin role before showing data
3. **Data Leakage**: Don't expose sensitive stats in errors
4. **CSRF Protection**: Use CSRF tokens if applicable
5. **Rate Limiting**: Apply rate limits to prevent abuse
6. **Audit Logging**: Log all admin access

## Next Steps

After implementing the home dashboard:

1. **Add More Metrics**: Real-time system health, latency percentiles
2. **Create Widgets**: Custom dashboard widgets for different roles
3. **Add Notifications**: Real-time alerts for critical events
4. **Build Reports**: Generate PDF/CSV reports of stats
5. **Implement Search**: Search routes, audit logs, etc.
6. **Add Export**: Export dashboard data in various formats
