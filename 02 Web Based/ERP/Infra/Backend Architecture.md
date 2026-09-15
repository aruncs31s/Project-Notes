# Backend Architecture

The Go backend of the College ERP project is designed following **Clean Architecture** and **Domain-Driven Design (DDD)** principles. It is structured to decouple core business logic from external frameworks, databases, and third-party APIs.

---

## Navigation
- [[Overview]] - Global infrastructure and docker architecture.
- [[Frontend Architecture]] - SPA UI system.
- [[Database Schema]] - Tables, primary keys, and relationships.
- [[../Registration/Registration Module|Registration Module]] - Gale-Shapley and OCR module.

---

## Clean Architecture Layers
The `backend/internal` folder contains the core logic partitioned into four clean layers:

```
backend/internal/
├── domain/            # Layer 1: Domain Entities & Repositories (Independent)
├── application/       # Layer 2: Core Use Cases & Application Services
├── infrastructure/    # Layer 3: DB Adapters, Casbin, MinIO, OCR, PDF
└── interfaces/        # Layer 4: HTTP Routes, Controllers, & Middlewares
```

### 1. Domain Layer (`internal/domain`)
Contains the core business model structs and repository interface specifications. It has zero external dependencies (except basic libraries like UUID or standard time packages).
- **academic/**: `AcademicYear` entity and repo interface.
- **college/**: `College`, `Course`, `CourseType`, `Batch`, `AdmissionCategory`, `AdmissionQuota`, `SeatBucket`, and `Seat` entities.
- **user/**: `User` profile entity and repo interface.
- **registration/**: `Application`, `Document`, `CoursePreference`, and `RankEntry` entities. Contains the Gale-Shapley matching domain logic.
- **notification/**: `Notification` templates and event definitions.
- **issue/**: `Issue` ticket and `Comment` entities.

### 2. Application Layer (`internal/application`)
Orchestrates domain models and executes workflows for use cases. Uses repository interfaces to interact with storage.
- **college/service.go**: CRUD operations for colleges, courses, and seat bucket limits.
- **registration/service.go**: Submission of application forms, uploading docs, verifying documents, and running rank allocations.
- **notification/service.go**: Async event dispatching to email/SMTP.
- **ocr/service.go**: Processing document files with Tesseract to extract text for verification.
- **pdf/service.go**: Building PDF exports of rank lists and applications.

### 3. Infrastructure Layer (`internal/infrastructure`)
Contains concrete technical adapters for datastores and external tools.
- **persistence/mysql/**: GORM implementation of repository interfaces. Manages schema migrations and connections.
- **persistence/redis/**: Redis wrapper for session/caching.
- **casbin/**: Casbin policy engine loading roles and rules from database.
- **storage/**: S3-compatible file storage adapter (configured to connect to MinIO).
- **ocr/**: Tesseract client executing optical character recognition.
- **pdf/**: PDF generation libraries compiling transcripts/rankings.

### 4. Interfaces Layer (`internal/interfaces`)
The external REST API gateway. Handles parsing HTTP requests, validation, and JSON serialization.
- **http/router.go**: Routes configuration for public, student-scoped, and admin-scoped paths.
- **http/handlers/**: Controllers split by domain context (e.g. `admin`, `auth`, `registration`, `superadmin`, `issue`).
- **http/middleware/**: HTTP interceptors:
  - `Auth()` - JWT parsing and authentication.
  - `Tenant()` - Extracts college slug from headers and resolves/attaches the database connection to request context.
  - `RequireRoles()` - Basic RBAC role checks.

---

## Key Core Mechanisms

### Context-Based Dynamic Multi-Tenancy
The system implements multi-tenancy at the database level (separate database schemas per tenant college).
1. The frontend client sends the college slug via the HTTP Header `X-Tenant-ID: demo`.
2. The `Tenant()` middleware intercepts the request:
   - Queries the master database to check if the college exists.
   - Extracts the database name (e.g., `college_<id>`).
3. Calls the database `Manager`'s `TenantDB(ctx, tenantID)` which:
   - Checks if a `*gorm.DB` connection pool for this tenant already exists in a thread-safe `sync.Map`.
   - If not, opens a new connection pool for the tenant schema and saves it in the map.
4. Attaches the `*gorm.DB` instance to the request context.
5. In GORM repository adapters, calling `r.resolver.Resolve(ctx)` retrieves this connection pool dynamically, ensuring all SQL queries stay partitioned inside the college's schema.

### Casbin RBAC/ABAC Access Control
Access to admin endpoints is guarded using **Casbin**:
- Policy schemas are stored dynamically in the MySQL master database.
- The authorization middleware evaluates access tuples `(user, domain/tenant, resource, action)` against the active policies.
- Custom roles and access rules can be edited dynamically on the admin dashboard and are reloaded instantly in memory using `infraCasbin.Enforcer.ReloadPolicy()`.
