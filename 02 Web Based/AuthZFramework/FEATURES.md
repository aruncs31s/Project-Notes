# ✨ Features Overview

> [!abstract] Comprehensive Feature Documentation
> Complete guide to all features in the ETLAB Authorization Framework

[[Auth Z Framework|← Back to Home]]

## Table of Contents

- [Core Authorization Features](#core-authorization-features)
- [Admin Dashboard Features](#admin-dashboard-features)
- [API Management Features](#api-management-features)
- [Monitoring & Analytics Features](#monitoring--analytics-features)
- [Security Features](#security-features)
- [Developer Features](#developer-features)

## 🔑 Core Authorization Features

### 1. Casbin-Based RBAC

The framework uses Casbin, an industry-standard authorization library, to provide flexible and powerful role-based access control.

**Capabilities:**
- Define roles and permissions using policy files
- Hierarchical role inheritance
- Dynamic policy loading and reloading
- Support for custom policy formats (CSV, JSON, YAML)
- Efficient policy evaluation engine

**Example Policy:**
```csv
p, admin, /api/*, GET
p, admin, /api/*, POST
p, user, /api/profile, GET
g, alice, admin
g, bob, user
```

**Use Cases:**
- Multi-role user management
- Hierarchical organization structures
- Dynamic permission assignments
- Fine-grained access control

### 2. Route Metadata Management

Comprehensive metadata system for API endpoints that extends beyond basic Casbin policies.

**Features:**
- **Path & Method**: Define exact endpoint paths and HTTP methods
- **Description**: Human-readable endpoint descriptions
- **Allowed Roles**: Specify which roles can access the endpoint
- **API Versioning**: Track and manage API versions
- **Tags**: Categorize endpoints for better organization
- **Public/Protected Status**: Mark endpoints as publicly accessible or protected
- **Ownership Checks**: Enable resource-level access control
- **Audit Requirements**: Mark sensitive endpoints for audit logging
- **Deprecation Info**: Track deprecated endpoints and their replacements

**Configuration Example:**
```json
{
  "path": "/api/users/{id}",
  "method": "GET",
  "description": "Get user by ID",
  "allowed_roles": ["admin", "manager"],
  "api_version": "v1",
  "tags": ["users", "read"],
  "is_public": false,
  "ownership_check": true,
  "audit_required": true,
  "deprecated": false
}
```

**Benefits:**
- Self-documenting API
- Centralized permission management
- Easy onboarding for new developers
- Compliance tracking

### 3. JWT Authentication

Secure token-based authentication using JSON Web Tokens.

**Features:**
- Token generation with custom claims
- Secure token validation
- Configurable expiration times
- Role and permission embedding in tokens
- Refresh token support (via custom implementation)

**Token Claims:**
```go
{
  "username": "alice",
  "role": "admin",
  "user_id": "user_123",
  "exp": 1234567890,
  "iat": 1234567890
}
```

**Integration:**
```go
// Tokens are automatically validated by middleware
// Access user info from context
if claims, ok := c.Get("claims"); ok {
    tokenClaims := claims.(jwt.MapClaims)
    username := tokenClaims["username"].(string)
}
```

### 4. Ownership Checks

Resource-level access control to ensure users can only access their own resources.

**How It Works:**
- Enable `ownership_check` in route metadata
- Framework validates that the requesting user owns the resource
- Supports custom ownership validation logic
- Works with user IDs, organization IDs, etc.

**Example:**
```
User Bob requests: GET /api/users/123/profile
- Framework checks if Bob's user_id == 123
- If yes, allow access
- If no, deny access even if Bob has the 'user' role
```

**Use Cases:**
- User profile access
- Document ownership
- Private resource access
- Multi-tenant data isolation

### 5. Audit Logging

Comprehensive tracking of all authorization decisions and API access.

**What's Logged:**
- Timestamp of request
- User identity
- Endpoint accessed
- HTTP method
- Authorization decision (allow/deny)
- Response time
- Status code
- Error details (if any)

**Benefits:**
- Compliance with regulations (GDPR, SOC 2, etc.)
- Security incident investigation
- Usage pattern analysis
- Performance debugging

## 🏛️ Admin Dashboard Features

### 1. Modern Web UI

A beautiful, responsive web interface for managing and monitoring the authorization system.

**Characteristics:**
- **Responsive Design**: Works on desktop, tablet, and mobile
- **Dark Mode**: Automatic dark mode support with toggle
- **Modern UI**: Built with Tailwind CSS
- **Icon System**: Font Awesome integration
- **Fast Loading**: Optimized assets and rendering
- **Accessibility**: WCAG compliant design

### 2. Login & Authentication

Secure admin access with session management.

**Features:**
- Username/password authentication
- JWT token generation
- Secure session cookies
- Automatic session expiration
- Remember me functionality (via localStorage)
- Logout with session cleanup

**Security:**
- HTTP-only cookies for session
- JWT token validation
- Configurable session timeout
- Secure password requirements (min 6 characters)

### 3. Home Dashboard

Central hub with system overview and quick access to all features.

**Widgets:**
- **Total Routes**: Count of configured API endpoints
- **Audit Logs**: Total authorization events
- **Total Requests**: API calls processed
- **Average Response Time**: Performance metric

**Quick Access Cards:**
- API Analytics
- Route Metadata Management
- RBAC Policies
- Rate Limiting
- Audit Logs
- Admin Authentication

**System Information:**
- Framework version
- Current admin user
- System status indicators

### 4. API Analytics Page

Comprehensive API usage and performance monitoring.

**Metrics Displayed:**

**Top Endpoints:**
- Most frequently accessed APIs
- Request count per endpoint
- Percentage of total traffic
- Visual ranking

**Slowest Endpoints:**
- APIs with highest response times
- Average response time per endpoint
- Performance bottleneck identification
- Optimization opportunities

**Most Errored Endpoints:**
- APIs with highest error rates
- Error count and percentage
- Common error patterns
- Reliability insights

**Usage Summary:**
- Total requests processed
- Average response time
- Error rate percentage
- Success rate

**Trend Data:**
- 7-day usage trends
- Request volume over time
- Performance trends
- Error rate trends

**Interactive Features:**
- Click to drill down into endpoint details
- Filter by date range
- Export analytics data
- Real-time updates

### 5. Route Metadata Management Page

Visual interface for viewing and managing API route configurations.

**Features:**

**Route List:**
- Tabular view of all routes
- HTTP method badges with color coding
- Path and description display
- Allowed roles visualization
- Public/Protected status indicators
- API version information

**Route Details:**
- Expandable detail view
- Tags display
- Feature flags (ownership check, audit required)
- Deprecation information
- Replacement endpoint info

**Actions:**
- Toggle route details
- Export routes as JSON
- Import routes from JSON
- Edit route metadata (view only, edit via config files)

**Visual Elements:**
- Color-coded HTTP methods (GET=blue, POST=green, DELETE=red, etc.)
- Role badges
- Status indicators
- Feature icons

## 🔧 API Management Features

### 1. API Usage Tracking

Automatic tracking of every API request.

**Tracked Information:**
- Endpoint path
- HTTP method
- User identity
- Timestamp
- Response time
- Status code
- Error details
- User agent
- IP address

**Storage:**
- SQLite database by default
- Configurable retention period
- Automatic cleanup of old logs
- Indexed queries for fast retrieval

### 2. Performance Monitoring

Real-time monitoring of API performance.

**Metrics:**
- **Response Time**: Min, max, average per endpoint
- **Throughput**: Requests per second/minute/hour
- **Error Rates**: Percentage of failed requests
- **Status Codes**: Distribution of HTTP status codes
- **Latency Percentiles**: P50, P95, P99

**Alerting:**
- Slow endpoint detection
- High error rate detection
- Unusual traffic patterns
- Performance degradation alerts

### 3. Route Configuration

JSON-based route configuration system.

**Configuration File:** `application/routes/enterprise_route_metadata.json`

**Format:**
```json
{
  "routes": [
    {
      "path": "/api/endpoint",
      "method": "GET",
      "description": "Endpoint description",
      "allowed_roles": ["role1", "role2"],
      "api_version": "v1",
      "tags": ["tag1", "tag2"],
      "is_public": false,
      "ownership_check": false,
      "audit_required": true,
      "deprecated": false,
      "deprecated_reason": "",
      "replaced_by": ""
    }
  ]
}
```

**Management:**
- Export current configuration
- Import from JSON file
- Version control friendly
- Validation on load
- Hot reload support (restart required)

### 4. API Versioning

Track and manage multiple API versions.

**Features:**
- Version field in route metadata
- Support for multiple versions of same endpoint
- Deprecation tracking
- Replacement endpoint mapping
- Version-specific documentation

**Example:**
```
v1: /api/users (deprecated, replaced by v2)
v2: /api/v2/users (current)
```

## 📊 Monitoring & Analytics Features

### 1. Usage Analytics

Comprehensive usage statistics and trends.

**Reports:**
- **Daily Usage**: Requests per day
- **Weekly Trends**: 7-day rolling trends
- **Monthly Reports**: Aggregated monthly statistics
- **Peak Times**: Identify high-traffic periods
- **User Activity**: Per-user usage statistics

**Visualization:**
- Line charts for trends
- Bar charts for rankings
- Pie charts for distribution
- Tables for detailed data

### 2. Performance Analytics

Detailed performance metrics and insights.

**Analysis:**
- **Endpoint Performance**: Response time per endpoint
- **Performance Trends**: Changes over time
- **Bottleneck Identification**: Slow endpoints
- **Optimization Opportunities**: Performance improvement suggestions

### 3. Error Analytics

Track and analyze API errors.

**Error Tracking:**
- Error count per endpoint
- Error rate percentage
- Common error types
- Error messages
- Stack traces (if enabled)

**Analysis:**
- Most error-prone endpoints
- Error patterns
- Temporal error distribution
- Error root cause analysis

### 4. Audit Trail

Complete audit trail of all authorization decisions.

**Audit Records:**
- All access attempts (allowed and denied)
- User identity
- Resource accessed
- Authorization decision
- Timestamp
- Context information

**Use Cases:**
- Security investigations
- Compliance reporting
- Access pattern analysis
- Anomaly detection

## 🛡️ Security Features

### 1. Authentication & Authorization

Multi-layered security approach.

**Layers:**
1. **JWT Token Validation**: Verify token signature and expiration
2. **Casbin Policy Check**: Enforce role-based permissions
3. **Route Metadata Validation**: Check route-specific requirements
4. **Ownership Verification**: Validate resource ownership
5. **Audit Logging**: Record all access attempts

### 2. Admin Security

Secure admin dashboard access.

**Features:**
- Environment-based credentials
- Session management
- JWT token authentication
- Logout with session cleanup
- No hardcoded passwords
- Configurable password requirements

### 3. Data Protection

Protect sensitive data and credentials.

**Measures:**
- Environment variable configuration
- No credentials in code
- HTTP-only cookies
- Secure token storage
- Database encryption support (via GORM)

### 4. Request Validation

Validate all incoming requests.

**Validation:**
- JWT token format and signature
- Required headers
- Request payload
- Authorization header
- Content-Type validation

## 👨‍💻 Developer Features

### 1. Easy Integration

Simple API for integration with existing applications.

**Minimal Setup:**
```go
// 3 lines to add full authorization
etlabauthzframework.InitAuthZModule(nil, nil)
r = etlabauthzframework.SetAuthZMiddleware(r)
defer etlabauthzframework.StopAuthZModule()
```

### 2. Middleware Architecture

Composable middleware for flexible integration.

**Available Middleware:**
- **Authorization Middleware**: Enforce access control
- **API Tracking Middleware**: Track API usage
- **Both can be used independently or together**

### 3. Logging

Structured logging with Zap.

**Features:**
- Different log levels (Debug, Info, Warn, Error)
- Structured logging format
- Customizable log output
- Performance optimized
- Context-aware logging

**Usage:**
```go
logger := etlabauthzframework.GetLogger()
logger.Info("Application started")
logger.Error("Error occurred", zap.Error(err))
```

### 4. Configuration Management

Flexible configuration system.

**Configuration Sources:**
- Environment variables (.env file)
- Configuration files (JSON, CSV)
- Programmatic configuration
- Database configuration (for dynamic settings)

### 5. Database Support

GORM-based database abstraction.

**Supported Databases:**
- SQLite (default)
- PostgreSQL (via GORM driver)
- MySQL (via GORM driver)
- SQL Server (via GORM driver)

**Features:**
- Auto-migration
- Relationship management
- Query optimization
- Connection pooling

### 6. Extensibility

Designed for extension and customization.

**Extension Points:**
- Custom authorization policies
- Custom middleware
- Custom audit logging
- Custom analytics
- Custom UI components

## 🚀 Upcoming Features

> [!todo] Planned Features
> Features scheduled for future releases

### Planned Features

1. **User Management UI**
   - Add, edit, delete users
   - Assign roles to users
   - View user permissions
   - User activity logs

2. **Role Management UI**
   - Create custom roles
   - Define role permissions
   - Role hierarchy management
   - Role templates

3. **Policy Editor UI**
   - Visual policy editor
   - Test policy rules
   - Import/export policies
   - Policy versioning

4. **Rate Limiting UI**
   - Configure rate limits per endpoint
   - Per-user rate limits
   - Per-role rate limits
   - Real-time limit monitoring

5. **External Identity Provider Integration**
   - OAuth2 support
   - SAML integration
   - LDAP/Active Directory
   - Social login (Google, GitHub, etc.)

6. **Advanced Analytics**
   - Custom dashboards
   - Anomaly detection
   - Predictive analytics
   - Cost analysis

7. **API Gateway Features**
   - Request transformation
   - Response caching
   - Load balancing
   - Circuit breaker

8. **Webhook Support**
   - Authorization event webhooks
   - Audit log webhooks
   - Custom webhook triggers
   - Webhook retry logic

## Feature Comparison

| Feature | Status | UI Support | API Support |
|---------|--------|------------|-------------|
| JWT Authentication | ✅ Available | ✅ Yes | ✅ Yes |
| Casbin RBAC | ✅ Available | ⚠️ View Only | ✅ Yes |
| Route Metadata | ✅ Available | ✅ Yes | ✅ Yes |
| API Usage Tracking | ✅ Available | ✅ Yes | ✅ Yes |
| Performance Monitoring | ✅ Available | ✅ Yes | ✅ Yes |
| Audit Logging | ✅ Available | ✅ Yes | ✅ Yes |
| Admin Dashboard | ✅ Available | ✅ Yes | N/A |
| Dark Mode | ✅ Available | ✅ Yes | N/A |
| User Management | 🚧 Planned | 🚧 Planned | 🚧 Planned |
| Role Assignment | 🚧 Planned | 🚧 Planned | 🚧 Planned |
| Policy Editor | 🚧 Planned | 🚧 Planned | ✅ Yes |
| Rate Limiting | ⚠️ Code Only | 🚧 Planned | ✅ Yes |
| External Identity | 🚧 Planned | 🚧 Planned | 🚧 Planned |

**Legend:**
- ✅ Available: Fully implemented and ready to use
- ⚠️ View Only: Can view but not edit via UI
- ⚠️ Code Only: Configuration via code/files only
- 🚧 Planned: Scheduled for future release

---

For detailed usage instructions, see the [README.md](README.md) file.

For dashboard-specific documentation, see the [DASHBOARD.md](DASHBOARD.md) file.
