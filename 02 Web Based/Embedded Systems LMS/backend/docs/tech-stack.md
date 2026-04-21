# Tech Stack

## Backend Technology

| Component | Technology | Version |
|-----------|-----------|--------|
| Language | Go | 1.25.6 |
| Framework | Gin | 1.11.0 |
| ORM | GORM | 1.31.1 |
| Database | PostgreSQL | - |
| JWT | golang-jwt/jwt | 5.3.1 |
| Docs | Swagger | 1.16.6 |
| Logging | Uber Zap | 1.27.1 |
| Password | golang/crypto | bcrypt |

## Project Structure

```
gcek_lms_backend/
├── cmd/
│   └── seed/           # Database seeder
├── docs/               # Swagger docs
├── internal/
│   ├── dto/           # Data transfer objects
│   ├── handler/       # HTTP handlers
│   ├── middleware/    # Auth middleware
│   ├── model/         # Database models
│   ├── repository/   # Database queries
│   ├── routes/       # Route definitions
│   └── service/      # Business logic
├── pkg/
│   ├── certgen/       # Certificate generation
│   ├── coderunner/   # Code execution
│   ├── config/       # Configuration
│   ├── database/     # DB connection
│   └── ocr/         # OCR service
├── utils/            # Utilities
├── uploads/         # File storage
└── templates/       # HTML templates
```

## Key Packages

| Package | Purpose |
|---------|--------|
| `chromedp` | Chrome automation for certs |
| `gorilla/websocket` | WebSocket support |
| `godotenv` | Environment variables |
| `lumberjack` | Log rotation |

## Environment Variables

| Variable | Description |
|----------|------------|
| `JWT_SECRET` | JWT signing secret |
| `DATABASE_URL` | PostgreSQL connection |
| `PORT` | Server port |