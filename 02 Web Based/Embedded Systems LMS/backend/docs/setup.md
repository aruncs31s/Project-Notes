# Setup and Installation

This guide will walk you through setting up the ESDC LMS Backend for local development.

## Prerequisites

- **Go 1.25+**: Ensure you have Go installed on your machine.
- **PostgreSQL**: The application uses PostgreSQL as its primary database.
- **Chromedp/Chrome**: Required for certificate PDF generation.
- **Air (Optional)**: For live-reloading during development.

## Environment Variables

The application relies on environment variables for configuration. Create a `.env` file in the root directory:

```env
# Database Configuration
DB_HOST=localhost
DB_USER=postgres
DB_PASSWORD=yourpassword
DB_NAME=esdc_lms
DB_PORT=5432

# Server Configuration
SERVER_PORT=8080
SERVER_URL=http://localhost:8080

# Logging
LOG_DIR=logs
LOG_LEVEL=info

# Media and Uploads
MEDIA_URL=http://localhost:8080/uploads
UPLOAD_DIR=uploads

# Authentication
JWT_SECRET=your_super_secret_jwt_key
```

## Running the Project

1. **Install Dependencies**
   ```bash
   go mod download
   ```

2. **Run with Air (Live Reload)**
   If you have Air installed, simply run:
   ```bash
   air
   ```

3. **Run normally**
   ```bash
   go run cmd/server/main.go
   ```

4. **Accessing the API**
   The server will start on the port specified in your `.env` (default is `8080`).
   Health check: `http://localhost:8080/api/health`
