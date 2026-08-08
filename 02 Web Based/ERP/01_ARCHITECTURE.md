# System Architecture & Technical Specifications

## 1. Overview
This Enterprise Resource Planning (ERP) platform is a multi-tenant university admission and academic management system. It provides database-isolated tenant environments (`college_<tenant_id>`), dynamic registration form builders, automated Gale-Shapley (Deferred Acceptance) ranking, and granular RBAC.

## 2. Infrastructure & Core Dependencies
- **Logging**: `github.com/aruncs31s/gologger` for structured log management.
- **Exporting**: `github.com/aruncs31s/goexport` for CSV/Excel/PDF reporting and data exports.
- **Redis & Caching**: `github.com/aruncs31s/goredis` for multi-tenant cache isolation, singleflight execution, and tag-based invalidations.
- **Role & Authorization**: Casbin (`github.com/casbin/casbin/v2`), GORM adapter (`gorm-adapter/v3`), Redis, and MySQL.
- **Session Management**: Super Admin session revocation and session tracking stored in Redis/DB.
- **Authentication**: JWT with token rotation (Access Token + Refresh Token flow).
- **Middleware Security**: Rate limiting, Request ID tracking (`X-Request-ID`), Tenant resolution, and CORS.
- **Feature Flags**: Dynamic feature/submodule toggles controlled via college/tenant configurations.

## 3. Frontend Architecture Rules (`frontend/AI_INSTRUCT/GUIDELINES.md`)
1. **Module-Based Directory Layout**:
   - Features reside in `src/modules/<module-name>/`.
   - Single-module helpers stay within that module's root.
   - Cross-module elements reside in `src/components/`.
2. **Separation of Concerns & SOLID / DIP**:
   - Views (`.tsx`), logic (`.logic.ts` / `use[Name].ts`), data access (`.repository.ts`), and styling (`.module.scss`) are decoupled.
3. **Scoped SCSS**:
   - Page and component styles are strictly scoped (`.module.scss`) to prevent CSS leakage.
   - Global tokens, mixins, and Nord color palettes live in `src/styles/`.
4. **Common UI Library**:
   - Loaders, toasts, error messages, export modals, and download buttons live in `src/components/`.
