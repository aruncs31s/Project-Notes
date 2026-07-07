# Scalable Extension Guidelines

This guide details the architectural standards for adding new modules or extending existing features in both the Frontend (React + TypeScript) and Backend (Go clean architecture). 

Following these guidelines ensures the code remains highly decoupled, easy to test, and supports **fluxible (flexible) coding** with shared domain constraints.

---

## Navigation
- [[Overview]] - Global infrastructure and monorepo files.
- [[Backend Architecture]] - Go Clean Architecture backend.
- [[Frontend Architecture]] - SPA UI routing and components.
- [[Database Schema]] - Tables, primary keys, and relationships.

---

## 1. Frontend Extension Pattern (Clean Design)

When adding a new feature module (e.g. `billing`, `registration`, `superadmin`), do not make direct Axios calls from presentational React components. Follow the three-tier separation of concerns:

```
┌────────────────────────────────────────────────────────┐
│                      UI Component                      │
│      (renders UI, imports local hooks/styles/SCSS)      │
└───────────────────────────┬────────────────────────────┘
                            │ uses
                            ▼
┌────────────────────────────────────────────────────────┐
│                      Custom Hook                       │
│    (handles React state, loaders, alerts, validation)  │
└───────────────────────────┬────────────────────────────┘
                            │ interacts with
                            ▼
┌────────────────────────────────────────────────────────┐
│                       Repository                       │
│      (abstract interface in domain, Axios in shared)    │
└────────────────────────────────────────────────────────┘
```

### Step A: Define Domain Constants & Interfaces
* If you have shared constraints (e.g. status lists, roles, rules), create or edit a file in `frontend/src/domain/` (such as `frontend/src/domain/user.ts`) containing exported constants. Do not reinvent or duplicate these keys.
* Define your repository contract interface inside `frontend/src/domain/repositories/` to specify standard CRUD behaviors:
  ```typescript
  // src/domain/repositories/courses.repository.ts
  import { Course } from '../../types'
  
  export interface CourseRepository {
    getCourses(tenantId: string): Promise<Course[]>
    createCourse(tenantId: string, course: Partial<Course>): Promise<Course>
  }
  ```

### Step B: Create concrete Repository Implementations
* Back the domain repository interface with network or storage adapters inside `frontend/src/shared/repositories/`:
  ```typescript
  // src/shared/repositories/courses.repository.impl.ts
  import { CourseRepository } from '../../domain/repositories/courses.repository'
  import axiosClient from '../api/axiosClient'
  import { Course } from '../../types'
  
  export class CourseRepositoryImpl implements CourseRepository {
    async getCourses(tenantId: string): Promise<Course[]> {
      const res = await axiosClient.get('/admin/courses', {
        headers: { 'X-Tenant-ID': tenantId }
      })
      return res.data.data
    }
    
    async createCourse(tenantId: string, course: Partial<Course>): Promise<Course> {
      const res = await axiosClient.post('/admin/courses', course, {
        headers: { 'X-Tenant-ID': tenantId }
      })
      return res.data.data
    }
  }
  export const courseRepository = new CourseRepositoryImpl()
  ```

### Step C: Write Custom Hooks for State Management
* Wrap state loaders, loading spinners, errors, and toast notifications into custom React hooks inside `frontend/src/shared/hooks/` or inside the feature module's local hooks directory:
  ```typescript
  // src/shared/hooks/useCourses.ts
  import { useState, useCallback, useEffect } from 'react'
  import { courseRepository } from '../repositories/courses.repository.impl'
  import { Course } from '../../types'
  import toast from 'react-hot-toast'
  
  export function useCourses(tenantId: string) {
    const [courses, setCourses] = useState<Course[]>([])
    const [loading, setLoading] = useState(false)
    
    const loadCourses = useCallback(async () => {
      if (!tenantId) return
      setLoading(true)
      try {
        const data = await courseRepository.getCourses(tenantId)
        setCourses(data)
      } catch (err) {
        toast.error('Failed to load courses')
      } finally {
        setLoading(false)
      }
    }, [tenantId])
    
    useEffect(() => {
      loadCourses()
    }, [loadCourses])
    
    return { courses, loading, reload: loadCourses }
  }
  ```

### Step D: Separate Presentational Components & Styles
* Split large view files into smaller presentational components (e.g. modals, buttons, table grids) located in `src/modules/<feature>/components/`.
* Avoid placing massive blocks of styles or markup inside page views. Use the global styling tokens.
* Leverage custom SCSS classes in `src/styles/` referencing the **Nord Theme CSS variables** (`var(--primary)`, `var(--surface)`, `var(--text)`) to maintain cohesive aesthetics.

---

## 2. Backend Extension Pattern

When adding a new resource domain to the Go backend, map your classes according to clean Domain-Driven Design:

### Step A: Define Domain Models & Repo Interfaces
* Place the core structures, validation rules, and abstract database repository interfaces inside `backend/internal/domain/<resource>/`.
  * E.g. Define `Course` struct inside `entity.go` and `Repository` interface inside `repository.go`.

### Step B: Write Persistence Implementations
* Back the domain repository interface inside `backend/internal/infrastructure/persistence/mysql/` using GORM.
* Ensure you resolve db scopes dynamically through the tenant manager context (`ctxkeys.TenantDB`).

### Step C: Write Application Orchestrations
* Place transaction orchestrators, mailing events, and business validations inside `backend/internal/application/<resource>/service.go`.

### Step D: Register HTTP Router & Enforce RBAC
* Expose endpoints inside `backend/internal/interfaces/http/handlers/<resource>/`.
* Bind JSON request validation and construct HTTP response envelopes.
* Register routes inside `backend/internal/interfaces/http/router.go` and append Casbin enforcer rules in `infrastructure/casbin/enforcer.go` to secure endpoints.
