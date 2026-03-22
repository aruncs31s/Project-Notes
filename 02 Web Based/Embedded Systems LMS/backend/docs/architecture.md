# Architecture & Tech Stack

## Tech Stack
- **Language**: Go 1.25.6
- **Framework**: Gin (HTTP web framework)
- **Database**: PostgreSQL with GORM (ORM)
- **Authentication**: JWT (JSON Web Tokens)
- **Real-Time Config**: Gorilla WebSocket
- **Logging**: Uber Zap with Lumberjack rotation
- **Headless Browser**: Chromedp (for PDF / Certificate generation)

## Project Structure
The backend follows standard Go layout patterns, promoting a clean, layered architecture:

```text
gcek_lms_backend/
├── cmd/
│   ├── server/       # Main entry point for the API server
│   └── seed/         # Database seeding scripts
├── internal/         # Private application code
│   ├── dto/          # Data Transfer Objects (Request/Response shapes)
│   ├── handler/      # HTTP handlers (Controllers) parsing requests and formatting responses
│   ├── logger/       # Zap logger configuration
│   ├── middleware/   # Gin middlewares (Auth, Role-based access, CORS)
│   ├── model/        # Database models (GORM entities)
│   ├── repository/   # Data access layer (Database interactions)
│   ├── routes/       # API routing definitions
│   └── service/      # Business logic layer
├── pkg/              # Public reusable packages
│   ├── certgen/      # Certificate generation via ChromeDP
│   ├── coderunner/   # Sandbox execution logic for code assignments
│   ├── config/       # Environment configuration loading
│   ├── database/     # DB connection and setup
│   └── ocr/          # Optical Character Recognition client (if used for assignments)
├── docs/             # Application documentation
├── templates/        # HTML templates (e.g., certificates)
└── uploads/          # Local storage for uploaded files and generated certificates
```

## Layered Architecture
Each request generally flows through the following layers:
**Route -> Middleware(Auth/Role) -> Handler -> Service -> Repository -> Database**

This separation of concerns makes testing and refactoring significantly easier.
