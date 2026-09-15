# System Overview & Infrastructure

Welcome to the **College Education ERP** project documentation. This note serves as the entry point for the system's technical infrastructure, outlining the monorepo structure, containerized environments, and how the components link together.

## Vault Navigation
- **Architecture**:
  - [[Backend Architecture]] - Go Clean Architecture, DDD, Dynamic Connection Pooling, and Auth/Casbin.
  - [[Frontend Architecture]] - React, Vite, Zustand, module-based page structures, and CSS.
  - [[Database Schema]] - Master vs. Tenant schema layouts, entities, and relations.
  - [[Scalable Extension Guidelines]] - Coding guidelines for extending features using repository patterns, hooks, and shared constants.
- **Business Modules**:
  - [[../Registration/Registration Module|Registration Module]] - Application pipeline, OCR documents, and Gale-Shapley Seat Allocation.

---

## Monorepo Layout
The project is structured as a monorepo containing both the backend and frontend codebases, with orchestrators in the root.

```
.
├── backend/               # Go DDD Backend Service
├── frontend/              # Vite + React + TS Frontend
├── docker-compose.yml     # Infrastructure Service Orchestration
├── Makefile               # CLI Commands for local builds & orchestration
├── .env                   # Root Environment Configuration
└── .env.example
```

---

## Infrastructure Services
Local development is fully containerized using **Docker Compose**, running the following dependencies:

| Service | Technology | Port | Purpose |
| :--- | :--- | :--- | :--- |
| **Relational Database** | MySQL 8.0 | `3306` | Persistent store for Master and Tenant databases. |
| **Caching & Session** | Redis | `6379` | Temporary token store, rate limiting, and session caching. |
| **Object Storage** | MinIO (S3) | `9000` (API) / `9001` (Console) | Secure storage for student uploads (identity cards, grade sheets). |
| **Email Testing** | MailHog | `1025` (SMTP) / `8025` (Web UI) | Local SMTP server catching sent notifications for testing. |

---

## System Architecture Flow
```mermaid
graph TD
    Client[React SPA Client] -->|HTTPS Requests| Router[Gin Engine HTTP Router]
    
    subgraph Backend Core
        Router -->|1. Resolve Tenant Header| Middleware[Tenant Middleware]
        Middleware -->|2. Connection Pool| DBConn[MySQL Manager]
        Router -->|3. Route Request| Handlers[HTTP Handlers]
        Handlers -->|4. Orchestrate| AppServices[Application Services]
        AppServices -->|5. Business Logic| Domain[Domain Layer]
    end
    
    subgraph Databases & Services
        DBConn -->|Read/Write| MasterDB[(MySQL masterDB)]
        DBConn -->|Dynamic Schema| TenantDB[(MySQL college_id)]
        AppServices -->|Cache Token/Config| RedisStore[(Redis Cache)]
        AppServices -->|Store files| MinioStore[(MinIO Blob)]
        AppServices -->|Send alerts| SMTPMail[MailHog SMTP]
    end
    
    classDef client fill:#4f46e5,stroke:#fff,stroke-width:2px,color:#fff;
    classDef server fill:#0f172a,stroke:#38bdf8,stroke-width:2px,color:#fff;
    classDef store fill:#1e293b,stroke:#a855f7,stroke-width:2px,color:#fff;
    class Client client;
    class Router,Middleware,Handlers,AppServices,Domain server;
    class MasterDB,TenantDB,RedisStore,MinioStore,SMTPMail,DBConn store;
```

---

## Global Automation Commands (Makefile)
The root `Makefile` provides immediate tools for launching, seeding, and verifying the application:

- `make up` - Launches MySQL, Redis, MinIO, and MailHog containers in background.
- `make down` - Stops all running docker containers.
- `make backend-run` - Starts the Go server locally on port `8082`.
- `make frontend-run` - Starts the Vite dev server on port `3001`.
- `make seed-db` - Runs the Go seeder tool (`cmd/seed`) to populate database tenants (`demo` college), roles, users, and dummy applications.
- `make test` - Executes Go package unit tests.
