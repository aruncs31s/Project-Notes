# ⚡ Quick Start Guide

> [!success] Get Started in 5 Minutes
> Complete setup guide for the ETLAB Authorization Framework

[[Auth Z Framework|← Back to Home]]

## Prerequisites

> [!info] Requirements
> - Go 1.25.3 or higher
> - Basic understanding of Go and web development
> - Text editor or IDE

## Step 1: Install the Framework

```bash
go get github.com/aruncs31s/etlabauthzframework
```

## Step 2: Create Your Application

Create a new file `main.go`:

```go
package main

import (
    "github.com/aruncs31s/etlabauthzframework"
    "github.com/gin-gonic/gin"
)

func main() {
    // Initialize the authorization framework
    etlabauthzframework.InitAuthZModule(nil, nil)
    defer etlabauthzframework.StopAuthZModule()

    // Initialize API usage tracking
    etlabauthzframework.InitUsageTracking()

    // Create Gin router
    r := gin.Default()

    // Apply authorization middleware
    r = etlabauthzframework.SetAuthZMiddleware(r)
    
    // Apply API tracking middleware
    r = etlabauthzframework.SetApiTrackingMiddleware(r)

    // Setup admin UI dashboard
    r = etlabauthzframework.SetupUI(r)

    // Add your application routes
    r.GET("/api/hello", func(c *gin.Context) {
        c.JSON(200, gin.H{"message": "Hello from ETLAB AuthZ Framework!"})
    })

    // Start server
    r.Run(":8080")
}
```

## Step 3: Configure Environment

Create a `.env` file in the same directory:

```env
# JWT Secret - Use a long, random string (min 32 characters recommended)
JWT_SECRET=your-very-secure-jwt-secret-key-here-minimum-32-characters

# Admin Dashboard Credentials
ADMIN_USERNAME=admin
ADMIN_PASSWORD=admin123
```

> [!warning] Security
> Change the password in production!

## Step 4: Run Your Application

```bash
go run main.go
```

You should see output like:
```
[GIN-debug] Listening and serving HTTP on :8080
```

## Step 5: Access the Dashboard

1. Open your browser and navigate to:
   ```
   http://localhost:8080/admin-ui/login
   ```

2. Login with credentials from your `.env` file:
   - Username: `admin`
   - Password: `admin123`

3. Explore the dashboard:
   - **Home**: View system statistics
   - **API Analytics**: Monitor API usage and performance
   - **Route Metadata**: Manage API endpoint configurations
   - **Role Management**: View roles and user assignments

## What You Get Out of the Box

> [!check] Included Features
> - ✅ **Authentication**: JWT-based secure authentication
> - ✅ **Authorization**: Casbin-powered RBAC
> - ✅ **API Tracking**: Automatic usage monitoring
> - ✅ **Admin Dashboard**: Beautiful web interface
> - ✅ **Dark Mode**: Automatic theme switching
> - ✅ **Analytics**: Real-time API performance metrics  

## Next Steps

### 1. Configure Routes

Create `application/routes/enterprise_route_metadata.json`:

```json
{
  "routes": [
    {
      "path": "/api/hello",
      "method": "GET",
      "description": "Hello endpoint",
      "allowed_roles": ["user", "admin"],
      "api_version": "v1",
      "tags": ["greeting"],
      "is_public": true,
      "ownership_check": false,
      "audit_required": false,
      "deprecated": false
    }
  ]
}
```

### 2. Configure Casbin Policies

Create `config/casbin_rbac_model.conf`:

```conf
[request_definition]
r = sub, obj, act

[policy_definition]
p = sub, obj, act

[role_definition]
g = _, _

[policy_effect]
e = some(where (p.eft == allow))

[matchers]
m = g(r.sub, p.sub) && r.obj == p.obj && r.act == p.act
```

Create `config/casbin_rbac_policy.csv`:

```csv
p, admin, /api/*, GET
p, admin, /api/*, POST
p, admin, /api/*, PUT
p, admin, /api/*, DELETE
p, user, /api/hello, GET
g, alice, admin
g, bob, user
```

### 3. Add Protected Routes

```go
// This route requires authentication and authorization
r.GET("/api/admin/users", func(c *gin.Context) {
    // Only accessible to users with 'admin' role
    c.JSON(200, gin.H{
        "users": []string{"alice", "bob", "charlie"},
    })
})

// This route is public (configure in route metadata)
r.GET("/api/public/status", func(c *gin.Context) {
    c.JSON(200, gin.H{"status": "healthy"})
})
```

## Common Issues and Solutions

### Issue: Dashboard won't load
**Solution**: Make sure you called `etlabauthzframework.SetupUI(r)` before starting the server.

### Issue: Login fails
**Solution**: Check that `.env` file exists and contains correct credentials.

### Issue: Routes return 401 Unauthorized
**Solution**: 
1. Verify JWT token is being sent in Authorization header
2. Check Casbin policies allow the user's role to access the route
3. Ensure route metadata is configured correctly

### Issue: "permission denied" errors
**Solution**: Check your Casbin policy file and ensure the user's role has permission to access the endpoint.

## Testing the API

### Login and Get JWT Token

```bash
curl -X POST http://localhost:8080/admin-ui/login/json \
  -H "Content-Type: application/json" \
  -d '{"username":"admin","password":"admin123"}'
```

Response:
```json
{
  "success": true,
  "message": "Login successful",
  "jwt": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
  "sessionID": "..."
}
```

### Use JWT Token to Access Protected Routes

```bash
curl http://localhost:8080/api/admin/users \
  -H "Authorization: Bearer YOUR_JWT_TOKEN_HERE"
```

## Project Structure

After setup, your project should look like:

```
your-project/
├── main.go                          # Your application
├── .env                             # Configuration (don't commit!)
├── go.mod                           # Go modules
├── go.sum                           # Dependencies
├── config/                          # Casbin configuration
│   ├── casbin_rbac_model.conf
│   └── casbin_rbac_policy.csv
└── application/
    └── routes/
        └── enterprise_route_metadata.json
```

## Learn More

- **README.md**: Complete documentation with all features
- **FEATURES.md**: Detailed feature descriptions
- **DASHBOARD.md**: Dashboard usage guide
- **IMPLEMENTATION_SUMMARY.md**: Technical implementation details

## Example Use Cases

### Multi-Tenant SaaS Application
```go
// Configure ownership checks in route metadata
// Ensure users can only access their own resources
```

### Microservices Authorization
```go
// Use as centralized auth service
// Validate JWT tokens from other services
```

### Internal Admin Tools
```go
// Use dashboard to monitor API usage
// Track who accessed what and when
```

## Key Features to Explore

1. **Dashboard Home**: See real-time statistics
2. **API Analytics**: Identify slow endpoints and errors
3. **Route Management**: Configure endpoint permissions
4. **Role Management**: View role assignments
5. **Dark Mode**: Toggle in header
6. **Audit Logs**: Track authorization decisions

## Production Checklist

Before deploying to production:

- [ ] Change default admin password
- [ ] Use strong JWT secret (32+ characters)
- [ ] Configure proper Casbin policies
- [ ] Set up HTTPS/TLS
- [ ] Configure proper CORS settings
- [ ] Set up database backups
- [ ] Configure log rotation
- [ ] Set up monitoring/alerts
- [ ] Review security settings
- [ ] Test all authorization rules

## Getting Help

- **GitHub Issues**: [Report bugs or request features](https://github.com/aruncs31s/etlabauthzframework/issues)
- **Documentation**: Check README.md, FEATURES.md, and DASHBOARD.md
- **Examples**: See the Quick Start code above

## What's Next?

Now that you have the framework running:

1. Explore the dashboard at `http://localhost:8080/admin-ui`
2. Add your application's routes
3. Configure Casbin policies for your use case
4. Set up route metadata for all endpoints
5. Monitor API usage and performance
6. Customize the framework for your needs

## Contributing

Want to contribute? See the main README.md for contribution guidelines.

---

**Happy Coding!** 🚀

If you find this framework useful, please give it a ⭐ on GitHub!
