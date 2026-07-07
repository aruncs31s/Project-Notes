# Frontend Architecture

The frontend is a modern Single Page Application (SPA) built using **React 18**, **TypeScript**, and **Vite**. The design system employs sleek dark-mode glassmorphism with custom styling tokens.

---

## Navigation
- [[Overview]] - Global infrastructure and monorepo files.
- [[Backend Architecture]] - Go Clean Architecture backend.
- [[Database Schema]] - Tables, primary keys, and relationships.
- [[../Registration/Registration Module|Registration Module]] - Gale-Shapley and OCR module.

---

## Directory Structure
The UI code resides inside `frontend/src` and is organized modularly by functional area:

```
frontend/src/
├── app/               # Global configuration (Routing, Zustand Store)
├── modules/           # Module-specific pages, components, and local APIs
│   ├── auth/          # Login, Register, Password Recovery
│   ├── admin/         # Admin dashboards, Analytics, user CRUD, configs
│   ├── registration/  # Student application forms, uploads, rank statuses
│   ├── superadmin/    # Multi-tenant management, billing, DB status
│   └── issues/        # Support ticket list and comments feed
├── shared/            # Reusable core hooks, layout, global axios client
│   ├── api/           # Base Axios Client with interceptors
│   ├── components/    # Reusable UI (Modals, StatusBadges, Sidebar)
│   └── hooks/         # Custom hooks (useAuth, usePagination)
├── styles/            # SCSS variables, animations, and typography
└── types/             # Global TypeScript type definitions
```

---

## Core Technologies & Libraries

### 1. State Management (Zustand)
Global states like user sessions, active tokens, and current authentication state are managed via **Zustand** inside `src/app/store/authStore.ts`.
- The store is persisted automatically in `localStorage` under the name `auth-storage`.
- The `useAuth` hook in `src/shared/hooks/useAuth.ts` exposes selectors for login state, credentials updates, and tenant-based profile details.

### 2. HTTP Routing (React Router v6)
Routes are declared declaratively inside `src/app/router/index.tsx` using lazy loading (`React.lazy`) for faster initial page loads.
- **ProtectedRoute**: Intercepts routing changes to verify authentication and role access rules (e.g. restricts superadmin routes to `super_admin` only).
- **IndexRedirect**: Automatically forwards logged-in users to their correct workspace depending on their role:
  - `student` -> `/registration/my-application`
  - `super_admin` -> `/superadmin/colleges`
  - `staff` / `institute_admin` -> `/admin/dashboard`

### 3. Glassmorphic Style System
The UI utilizes CSS gradients, blur filters (`backdrop-filter: blur(20px)`), and thin glowing borders (`border: 1px solid rgba(255,255,255,0.08)`) to form a premium glassmorphic dark theme.
- **TailwindCSS**: Handles spacing, grid/flex layouts, and typography.
- **SCSS Components**: Used to declare core design tokens, scrollbars, spin animations, and custom dashboard card glows.
- **Framer Motion**: Power transitions between page loads and tab switches (`AnimatePresence mode="wait"`), along with hover state micro-animations.

---

## HTTP Request Architecture (Axios Client)
All communication with the REST backend is managed by the unified Axios instance at `src/shared/api/axiosClient.ts`. It includes request/response interceptors:

1. **Request Interceptor**:
   - Automatically extracts the active JWT token from the Zustand storage state and appends the Bearer token: `Authorization: Bearer <token>`.
   - Resolves the college domain slug from the host domain (or defaults to `demo` college tenant for local testing) and maps it to the custom header: `X-Tenant-ID: <slug>`. This header triggers the backend's dynamic database connection pool router.
2. **Response Interceptor**:
   - Watches for `401 Unauthorized` responses. If a session token expires, it fires a callback to clear the Zustand auth state and redirects the user to `/auth/login` to re-verify.
