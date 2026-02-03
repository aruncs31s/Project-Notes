---
title: "Add Analytics page with server statistics and traffic metrics"
status: "open"
created: "10/10/2025, 3:13:42 AM"
url: "https://github.com/aruncs31s/esdc-frontend/pull/4"
opened_by: "Copilot"
assignees: ["aruncs31s", "Copilot"]
updateMode: "none"
allowDelete: true
---

# Add Analytics page with server statistics and traffic metrics
## Overview

Implemented a comprehensive Analytics dashboard page that displays server statistics, traffic metrics, and performance insights for the ESDC platform.

## Features

### Server Statistics Dashboard
The Analytics page displays key platform metrics in modern glass-morphism cards:
- **Total Page Views** - Cumulative page views with formatted numbers (e.g., 15.4K)
- **Total Downloads** - Total resource downloads across the platform
- **Active Users** - Currently active users count
- **Server Uptime** - Server availability duration in readable format (e.g., 30d 0h)

### Traffic Analysis
Traffic statistics broken down by time periods:
- Daily traffic (requests/day)
- Weekly traffic (requests/week)
- Monthly traffic (requests/month)

### Server Resources Overview
Platform content statistics:
- Total Resources available
- Total Projects on the platform
- Total Challenges created

### Top Resources Table
Interactive ranked table showing:
- Resource rankings with visual badges (top 3 highlighted)
- Individual view counts with eye icons
- Download counts with download icons
- Total engagement metrics (views + downloads)

## Implementation Details

### Files Created
- **src/pages/Analytics.tsx** - Main Analytics dashboard component with responsive design
- **docs/ANALYTICSPAGE.md** - Comprehensive documentation including API specifications
- **docs/QUICKSTARTANALYTICS.md** - Quick start guide for users and developers

### Files Modified
- **src/services/api.ts** - Added analyticsAPI service with three endpoints:
  - getStats() - Fetches general platform statistics
  - getTopResources() - Fetches top performing resources
  - getTrafficStats() - Fetches traffic metrics
- **src/App.tsx** - Added /analytics route with authentication protection

## API Integration

The page integrates with backend endpoints:

GET /api/analytics/stats
GET /api/analytics/resources/top?limit10
GET /api/analytics/traffic


**Mock Data Fallback**: If backend endpoints are not available, the page automatically displays mock data, making it perfect for frontend development and demonstration.

## Access Control

- **Route**: /analytics
- **Authentication**: Required (any authenticated user)
- **Authorization**: Accessible to all logged-in users (not restricted to admins)

## Design

- Follows existing AdminPanel design patterns
- Glass-morphism cards with backdrop blur effects
- Responsive grid layouts for all screen sizes
- Color-coded icons from react-icons
- Number formatting with K/M suffixes for readability
- Catppuccin theme integration

## Screenshot

![Analytics Dashboard](https://github.com/user-attachments/assets/43c1d669-1d8a-48d5-938b-1fe44c917485)

The screenshot shows the complete Analytics dashboard with:
- Top section displaying 4 key metrics (page views, downloads, active users, uptime)
- Traffic statistics section with daily/weekly/monthly breakdowns
- Server resources overview
- Top resources table with rankings and engagement metrics

## Testing

-  Build successful (no compilation errors)
-  Linting passes (no new issues introduced)
-  TypeScript type-safe
-  Responsive design tested
-  Error handling with graceful fallbacks

## Usage

Users can access the Analytics page by navigating to /analytics after logging in. To add it to the navigation menu:

tsx
Link to"/analytics">Analytics/Link>


## Documentation

Complete documentation is available in:
- docs/ANALYTICSPAGE.md - Full feature documentation and API specifications
- docs/QUICKSTARTANALYTICS.md - Quick start guide with troubleshooting

## Future Enhancements

The documentation includes suggestions for future improvements:
- Interactive charts and graphs (line, pie, bar charts)
- Date range filters and comparisons
- Export functionality (CSV, PDF reports)
- Real-time updates via WebSocket
- Additional metrics and breakdowns
- Role-based analytics views

!-- START COPILOT CODING AGENT SUFFIX -->



details>

summary>Original prompt/summary>

> crate a new page called analytics where list server statistics and trafic etc


/details>



!-- START COPILOT CODING AGENT TIPS -->
- - -

 Let Copilot coding agent [set things up for you](https://github.com/aruncs31s/esdc-frontend/issues/new?title+Set+up+Copilot+instructions&bodyConfigure20instructions20for20this20repository20as20documented20in205BBest20practices20for20Copilot20coding20agent20in20your20repository5D28https://gh.io/copilot-coding-agent-tips292E0A0A3COnboard20this20repo3E&assigneescopilot)  coding agent works faster and does higher quality work when set up for your repo.



## Comments

### vercel[bot] commented (10/10/2025, 3:13:46 AM):

[vc]: #YLFkB/8sEJQ+F70Diiyca7PZfwqjiFyT9M5OIbrE6jk:eyJpc01vbm9yZXBvIjp0cnVlLCJ0eXBlIjoiZ2l0aHViIiwicHJvamVjdHMiOlt7Im5hbWUiOiJlcyIsImluc3BlY3RvclVybCI6Imh0dHBzOi8vdmVyY2VsLmNvbS9hcnVuLWNzcy1wcm9qZWN0cy9lcy9jUVhacDJGWkZIWTdNSEdRSFk1R2RDa3I3dkEzIiwicHJldmlld1VybCI6ImVzLWdpdC1jb3BpbG90LWFkZC1hbmFseXRpY3MtcGFnZS1hcnVuLWNzcy1wcm9qZWN0cy52ZXJjZWwuYXBwIiwibmV4dENvbW1pdFN0YXR1cyI6IkRFUExPWUVEIiwibGl2ZUZlZWRiYWNrIjp7InJlc29sdmVkIjowLCJ1bnJlc29sdmVkIjowLCJ0b3RhbCI6MCwibGluayI6ImVzLWdpdC1jb3BpbG90LWFkZC1hbmFseXRpY3MtcGFnZS1hcnVuLWNzcy1wcm9qZWN0cy52ZXJjZWwuYXBwIn0sInJvb3REaXJlY3RvcnkiOm51bGx9XX0
The latest updates on your projects. Learn more about [Vercel for GitHub](https://vercel.link/github-learn-more).

| Project | Deployment | Preview | Comments | Updated (UTC) |
| :- - - | :- - --- | :- - -- - - | :- - -- - -- | :- - -- - - |
| [es](https://vercel.com/arun-css-projects/es) | ![Ready](https://vercel.com/static/status/ready.svg) [Ready](https://vercel.com/arun-css-projects/es/cQXZp2FZFHY7MHGQHY5GdCkr7vA3) | [Preview](https://es-git-copilot-add-analytics-page-arun-css-projects.vercel.app) | [Comment](https://vercel.live/open-feedback/es-git-copilot-add-analytics-page-arun-css-projects.vercel.app?viapr-comment-feedback-link) | Oct 9, 2025 9:55pm |



---


