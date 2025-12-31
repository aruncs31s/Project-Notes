# 🏛️ Dashboard Guide

> [!note] Admin Dashboard Documentation
> Complete guide to using the ETLAB Authorization Framework admin dashboard

[[Auth Z Framework|← Back to Home]]

## Table of Contents

- [Getting Started](#getting-started)
- [Login Page](#login-page)
- [Home Dashboard](#home-dashboard)
- [API Analytics](#api-analytics)
- [Route Metadata Management](#route-metadata-management)
- [Navigation](#navigation)
- [Features](#features)
- [Tips and Tricks](#tips-and-tricks)

## Getting Started

### Accessing the Dashboard

> [!example] Dashboard URL
> `http://localhost:8080/admin-ui/login`

1. Start your application with the ETLAB framework integrated
2. Navigate to `http://localhost:8080/admin-ui/login` (adjust port as needed)
3. Log in with your admin credentials

### First-Time Setup

1. **Configure Admin Credentials**: Set up `.env` file with admin username and password:
   ```env
   ADMIN_USERNAME=admin
   ADMIN_PASSWORD=your-secure-password
   ```

2. **Configure JWT Secret**: Add a secure JWT secret (minimum 32 characters recommended):
   ```env
   JWT_SECRET=your-very-secure-jwt-secret-key-here
   ```

3. **Start Application**: Run your application with the framework initialized:
   ```go
   etlabauthzframework.InitAuthZModule(nil, nil)
   etlabauthzframework.InitUsageTracking()
   r = etlabauthzframework.SetupUI(r)
   ```

4. **Access Dashboard**: Open browser and navigate to the login page

## Login Page

### Overview

The login page is your entry point to the admin dashboard. It features a modern, secure authentication system.

**URL:** `/admin-ui/login`

### Features

- **Clean Interface**: Simple, uncluttered login form
- **Dark Mode**: Automatically matches your system preference
- **Secure Authentication**: JWT-based token generation
- **Error Handling**: Clear error messages for invalid credentials
- **Session Management**: Persistent sessions with secure cookies

### How to Login

1. Enter your admin username (configured in `.env`)
2. Enter your admin password (configured in `.env`)
3. Click "Login" button
4. Upon successful authentication, you'll be redirected to the home dashboard

### Security Features

> [!check] Security Measures
> - **JWT Tokens**: Secure token-based authentication
> - **HTTP-Only Cookies**: Session cookies protected from JavaScript access
> - **24-Hour Sessions**: Automatic session expiration after 24 hours
> - **No Credential Storage**: Passwords validated against environment variables only
> - **HTTPS Ready**: Works securely over HTTPS in production

### Troubleshooting

**Issue: "Invalid credentials" error**
- Solution: Verify credentials in `.env` file match your input
- Check that `.env` file is loaded properly
- Ensure ADMIN_USERNAME and ADMIN_PASSWORD are set correctly

**Issue: Cannot access login page**
- Solution: Verify your application is running
- Check that SetupUI() was called
- Ensure the port is correct in the URL

## Home Dashboard

### Overview

The home dashboard provides a comprehensive overview of your authorization system's status and quick access to all features.

**URL:** `/admin-ui`

### Key Metrics

The dashboard displays four key performance indicators:

#### 1. Total Routes
- **What it shows**: Number of API endpoints configured in your system
- **Location**: Top-left widget
- **Icon**: Blue route icon
- **Use**: Quickly verify all endpoints are registered

#### 2. Audit Logs
- **What it shows**: Total number of authorization events tracked
- **Location**: Top-center-left widget
- **Icon**: Green history icon
- **Use**: Monitor authorization activity and compliance

#### 3. Total Requests
- **What it shows**: Cumulative count of API calls processed
- **Location**: Top-center-right widget
- **Icon**: Purple tachometer icon
- **Use**: Track overall API usage

#### 4. Average Response Time
- **What it shows**: Mean response time across all endpoints in milliseconds
- **Location**: Top-right widget
- **Icon**: Red clock icon
- **Use**: Monitor system performance at a glance

### Features & Management Cards

The dashboard provides quick access cards to all major features:

#### 1. API Analytics (Active)
- **Icon**: Blue chart bar
- **Status**: Fully functional
- **Action**: Click to view detailed API usage and performance metrics
- **Use Case**: Monitor API health, identify issues, analyze trends

#### 2. Route Metadata (Active)
- **Icon**: Green route
- **Status**: Fully functional
- **Action**: Click to view and manage API route configurations
- **Use Case**: Review endpoint permissions, check configurations

#### 3. RBAC Policies (View Only)
- **Icon**: Purple lock
- **Status**: Configuration via files
- **Description**: Casbin-based role authorization policies
- **Use Case**: Understand that policies are managed via CSV/JSON files
- **Note**: UI editor planned for future release

#### 4. Rate Limiting (Code Configuration)
- **Icon**: Yellow gauge
- **Status**: Configuration in code
- **Description**: Control request rates per user/role
- **Use Case**: Understand rate limiting capabilities
- **Note**: UI configuration planned for future release

#### 5. Audit Logs (Active)
- **Icon**: Indigo shield
- **Status**: Clickable link (anchored to page section)
- **Description**: Track all authorization decisions
- **Use Case**: Security audits, compliance reporting

#### 6. Admin Authentication (Active)
- **Icon**: Pink user shield
- **Status**: Clickable link
- **Description**: Manage admin credentials and login
- **Use Case**: Update admin settings (currently via .env file)

### System Information

The dashboard header shows:
- **Framework Name**: ETLAB AuthZ
- **Tagline**: Enterprise Authorization Framework
- **Current User**: Displays logged-in admin username
- **Theme Toggle**: Switch between light and dark modes
- **Logout Button**: Sign out and clear session

### Navigation

From the home dashboard, you can:
- Click any active feature card to navigate to that section
- Use the theme toggle in the top-right to change appearance
- Click the logout icon to sign out
- View real-time statistics that update on page refresh

## API Analytics

### Overview

The API Analytics page provides comprehensive insights into API usage, performance, and reliability.

**URL:** `/admin-ui/api_analytics`

### Page Sections

#### 1. Usage Summary

Located at the top of the page, shows overall metrics:

- **Total Requests**: Cumulative number of API calls
- **Average Response Time**: Mean latency across all endpoints
- **Success Rate**: Percentage of successful requests (2xx status codes)
- **Error Rate**: Percentage of failed requests (4xx, 5xx status codes)
- **Generated At**: Timestamp of when analytics were generated

**Use Cases:**
- Quick health check of your API
- Identify if performance is degrading
- Monitor overall reliability

#### 2. Top Endpoints

Shows the most frequently accessed API endpoints.

**Information Displayed:**
- Endpoint path and HTTP method
- Request count
- Percentage of total traffic
- Visual ranking (1st, 2nd, 3rd, etc.)

**Method Color Coding:**
- 🔵 GET - Blue
- 🟢 POST - Green
- 🟡 PUT - Yellow
- 🟣 PATCH - Purple
- 🔴 DELETE - Red

**Use Cases:**
- Identify critical endpoints that need optimization
- Understand usage patterns
- Plan caching strategies
- Capacity planning

#### 3. Slowest Endpoints

Lists endpoints with the highest average response times.

**Information Displayed:**
- Endpoint path and HTTP method
- Average response time (ms)
- Performance indicator (visual ranking)

**Use Cases:**
- Identify performance bottlenecks
- Prioritize optimization efforts
- Database query optimization
- Caching opportunities

#### 4. Most Errored Endpoints

Shows endpoints with the highest error rates.

**Information Displayed:**
- Endpoint path and HTTP method
- Error count
- Error rate percentage
- Reliability indicator

**Use Cases:**
- Identify problematic endpoints
- Bug prioritization
- Error handling improvements
- Stability monitoring

#### 5. Usage Trends (7-Day)

Visual representation of API usage over the past week.

**Chart Features:**
- Date on X-axis
- Request count on Y-axis
- Line graph showing daily trends
- Hover tooltips with exact values

**Use Cases:**
- Identify usage patterns
- Detect anomalies
- Plan for traffic spikes
- Capacity planning

### Interactive Features

- **Responsive Design**: Works on all screen sizes
- **Dark Mode**: Seamless theme switching
- **Auto-Refresh**: Reload page to see updated metrics
- **Visual Ranking**: Color-coded rankings for quick insights

### How to Use

1. **Regular Monitoring**: Check daily to track API health
2. **Performance Review**: Weekly review of slowest endpoints
3. **Error Investigation**: Immediate investigation of high-error endpoints
4. **Trend Analysis**: Monitor weekly trends for capacity planning

### Navigation

- **Back Button**: Return to home dashboard (top-left)
- **Home Link**: Click ETLAB logo to return home
- **Logout**: Sign out (top-right)
- **Theme Toggle**: Switch light/dark mode (top-right)

## Route Metadata Management

### Overview

The Route Metadata Management page allows you to view all configured API endpoints and their authorization settings.

**URL:** `/admin-ui/route_metadata`

### Page Layout

#### Header
- **Back to Analytics**: Link to return to API Analytics
- **Page Title**: Route Metadata Management
- **Route Count**: Shows total number of configured routes
- **Theme Toggle**: Switch between light and dark modes
- **Logout Button**: Sign out of dashboard

#### Action Bar

Three buttons for route management:

1. **Add Route** (Blue)
   - Icon: Plus icon
   - Action: Opens modal to add new route
   - Note: Currently for viewing, actual implementation requires config file edit

2. **Export JSON** (Green)
   - Icon: Download icon
   - Action: Downloads current route configuration as JSON file
   - File: `enterprise_route_metadata.json`
   - Use: Backup, version control, sharing configurations

3. **Import JSON** (Purple)
   - Icon: Upload icon
   - Action: Upload and apply route configuration from JSON file
   - Format: Must match expected schema
   - Note: Requires application restart to take effect

#### File Location Display
Shows the location of the route metadata configuration file:
```
File: application/routes/enterprise_route_metadata.json
```

### Routes Table

#### Column Headers

1. **Toggle** (Chevron icon)
   - Expand/collapse route details
   - Click to show additional information

2. **Method**
   - HTTP method badge
   - Color-coded by method type
   - Visual indicator for quick scanning

3. **Path**
   - API endpoint path
   - Monospace font for clarity
   - Truncated with hover tooltip for long paths

4. **Description**
   - Human-readable endpoint description
   - Truncated with hover tooltip

5. **Roles**
   - Allowed roles for this endpoint
   - Color-coded role badges
   - Multiple badges if multiple roles
   - "None" displayed if no roles specified

6. **API Version**
   - Version identifier (e.g., v1, v2)
   - Helps track API versioning

7. **Public**
   - Status indicator
   - 🌐 Public (green): No authentication required
   - 🔒 Protected (gray): Authentication required

8. **Actions**
   - Edit button (blue pencil icon)
   - Deprecated badge (red, if applicable)

### Route Details (Expandable)

Click the chevron icon to expand and see:

#### Tags
- Categorization tags
- Helps organize endpoints
- Multiple tags displayed as badges

#### Features
- **Ownership Check**: 🛡️ Orange badge
  - Indicates resource ownership validation required
  - Users can only access their own resources

- **Audit Required**: 📜 Purple badge
  - Marks endpoint for audit logging
  - All access attempts logged

#### Deprecation Information
If route is deprecated:
- **Deprecation Reason**: Why it was deprecated
- **Replaced By**: New endpoint to use instead
- **Visual Warning**: Red exclamation badge

### Method Color Coding

| Method | Color | Badge |
|--------|-------|-------|
| GET | Blue | ![Blue](https://via.placeholder.com/15/3B82F6/000000?text=+) |
| POST | Green | ![Green](https://via.placeholder.com/15/10B981/000000?text=+) |
| PUT | Yellow | ![Yellow](https://via.placeholder.com/15/F59E0B/000000?text=+) |
| PATCH | Purple | ![Purple](https://via.placeholder.com/15/8B5CF6/000000?text=+) |
| DELETE | Red | ![Red](https://via.placeholder.com/15/EF4444/000000?text=+) |
| Other | Gray | ![Gray](https://via.placeholder.com/15/6B7280/000000?text=+) |

### Empty State

If no routes are configured:
- Centered message: "No routes configured"
- Instruction to add routes via configuration file
- Link to documentation

### How to Use

#### Viewing Routes
1. Navigate to Route Metadata Management page
2. Browse the routes table
3. Click chevron to expand route details
4. Review permissions, roles, and features

#### Exporting Routes
1. Click "Export JSON" button
2. JSON file downloads to your computer
3. File contains all route configurations
4. Use for backup or sharing

#### Importing Routes
1. Click "Import JSON" button
2. Select a valid JSON file
3. System validates the format
4. Alert confirms success or shows errors
5. Restart application to apply changes

#### Understanding Route Configuration

Each route has:
- **Path**: The endpoint URL
- **Method**: HTTP method (GET, POST, etc.)
- **Allowed Roles**: Who can access it
- **Public Status**: Authentication requirement
- **Ownership Check**: Resource ownership validation
- **Audit Required**: Logging requirement
- **Deprecation**: Endpoint lifecycle status

### Best Practices

1. **Regular Review**: Check route configurations weekly
2. **Document Endpoints**: Use clear descriptions
3. **Tag Appropriately**: Use tags for organization
4. **Mark Deprecations**: Always provide replacement info
5. **Audit Sensitive**: Mark sensitive endpoints for audit
6. **Version Properly**: Use API versioning consistently

## Navigation

### Global Navigation Elements

Available on all dashboard pages:

#### Header
- **Framework Logo**: Click to return to home
- **Page Title**: Current page name
- **User Info**: Displays logged-in username
- **Theme Toggle**: Switch light/dark mode
- **Logout Button**: Sign out

#### Breadcrumbs
Some pages include navigation breadcrumbs:
- API Analytics → Route Metadata
- Route Metadata → Back to Analytics

### Keyboard Shortcuts

While not explicitly implemented, standard browser shortcuts work:
- **Ctrl/Cmd + R**: Refresh page data
- **Ctrl/Cmd + W**: Close tab
- **Backspace**: Browser back button
- **Alt + Left Arrow**: Browser back

## Features

### Dark Mode

#### How It Works
- Automatically detects system preference
- Manual toggle available in header
- Preference saved in browser localStorage
- Applies to all pages consistently

#### Toggle Dark Mode
1. Locate the moon/sun icon in the header
2. Click to toggle between light and dark
3. Preference is saved automatically

#### Design
- **Light Mode**: Clean white background, dark text
- **Dark Mode**: Deep gray background, light text
- **Smooth Transitions**: Animated color changes
- **Accessibility**: High contrast ratios

### Responsive Design

#### Desktop (≥1024px)
- Full three-column layout for cards
- Wide table views
- All features visible

#### Tablet (768px - 1023px)
- Two-column card layout
- Horizontal scrolling for tables
- Optimized spacing

#### Mobile (< 768px)
- Single-column layout
- Stacked cards
- Scrollable tables
- Touch-friendly buttons

### Real-Time Updates

#### Auto-Refresh Strategy
Dashboard does not auto-refresh to save resources. To see updated data:
1. Manually refresh the page (F5 or Ctrl+R)
2. Navigate away and back
3. Use browser refresh button

#### What Updates
- All metrics recalculate on page load
- Analytics reflect all data up to current moment
- Route metadata loads from configuration file

### Export/Import

#### Export Functionality
- **Format**: JSON
- **Filename**: `enterprise_route_metadata.json`
- **Content**: Complete route configuration
- **Use Case**: Backup, version control, migration

#### Import Functionality
- **Accepted Format**: JSON only
- **Validation**: Schema validation on import
- **Error Handling**: Clear error messages
- **Note**: Restart required to apply changes

## Tips and Tricks

### Performance Optimization

1. **Monitor Slowest Endpoints Regularly**
   - Check weekly for performance degradation
   - Prioritize optimization of high-traffic slow endpoints
   - Consider caching strategies

2. **Error Rate Management**
   - Investigate endpoints with >5% error rate immediately
   - Set up alerts for critical endpoints (manual monitoring)
   - Review error patterns in audit logs

3. **Capacity Planning**
   - Use 7-day trends to predict growth
   - Scale before reaching capacity
   - Plan for traffic spikes based on patterns

### Security Best Practices

1. **Regular Credential Rotation**
   - Change admin password periodically
   - Use strong passwords (minimum 12 characters recommended)
   - Never share credentials

2. **Audit Log Review**
   - Review audit logs weekly for suspicious activity
   - Look for failed authorization attempts
   - Monitor access patterns

3. **Least Privilege Principle**
   - Only grant necessary roles to endpoints
   - Use public flag sparingly
   - Enable ownership checks for user resources

### Configuration Management

1. **Version Control**
   - Keep route metadata in version control
   - Export configuration before changes
   - Document changes in commit messages

2. **Environment Separation**
   - Use different credentials per environment
   - Maintain separate route configurations
   - Test changes in development first

3. **Documentation**
   - Write clear route descriptions
   - Document deprecations with reasons
   - Provide replacement endpoint info

### Troubleshooting

#### Issue: Dashboard Not Loading
- Check application is running
- Verify port number in URL
- Check browser console for errors
- Ensure SetupUI() was called in code

#### Issue: Login Failed
- Verify credentials in .env file
- Check .env file is loaded
- Ensure JWT_SECRET is set
- Try restarting application

#### Issue: Analytics Not Showing Data
- Verify InitUsageTracking() was called
- Check database connection
- Ensure middleware is applied
- Make some API requests to generate data

#### Issue: Routes Not Displaying
- Check route metadata JSON file exists
- Verify JSON format is correct
- Look for parsing errors in logs
- Restart application after config changes

### Useful Patterns

1. **Weekly Dashboard Review**
   - Monday: Review week's trends
   - Check for anomalies
   - Plan optimization work

2. **Before Deployment**
   - Export current route configuration
   - Review pending changes
   - Verify all endpoints are documented

3. **After Deployment**
   - Monitor error rates closely
   - Check performance metrics
   - Verify new routes are accessible

### Browser Compatibility

Tested and supported on:
- ✅ Chrome 90+
- ✅ Firefox 88+
- ✅ Safari 14+
- ✅ Edge 90+
- ⚠️ Internet Explorer: Not supported

### Recommended Workflow

1. **Morning Check**: Review dashboard metrics
2. **Weekly Review**: Analyze trends and performance
3. **Monthly Planning**: Capacity and optimization planning
4. **Continuous**: Monitor alerts and respond to issues

---

For API documentation and integration guide, see [README.md](README.md).

For comprehensive feature list, see [FEATURES.md](FEATURES.md).
