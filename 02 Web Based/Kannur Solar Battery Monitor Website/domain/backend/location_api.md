# Location API Documentation

This document provides detailed API documentation for the location-related endpoints in the SKVMS system.

## Base URL
All location endpoints are prefixed with `/api/locations`.

## Authentication
- **GET** endpoints: Public (no authentication required)
- **POST/PUT/DELETE** endpoints: Require JWT authentication via `Authorization: Bearer <token>` header

## Endpoints

### 1. List All Locations
**GET** `/api/locations`

Retrieves a list of all locations in the system.

#### Response
- **Status Code**: 200 OK
- **Body**:
```json
{
  "locations": [
    {
      "id": 1,
      "name": "Main Office",
      "description": "Primary office location",
      "code": "MAIN001"
    }
  ]
}
```

#### Error Responses
- **500 Internal Server Error**: Failed to load locations

---

### 2. Get Location by ID
**GET** `/api/locations/{id}`

Retrieves details of a specific location by its ID.

#### Parameters
- `id` (path): Location ID (integer)

#### Response
- **Status Code**: 200 OK
- **Body**:
```json
{
  "location": {
    "id": 1,
    "name": "Main Office",
    "description": "Primary office location",
    "code": "MAIN001"
  }
}
```

#### Error Responses
- **400 Bad Request**: Invalid location ID
- **404 Not Found**: Location not found
- **500 Internal Server Error**: Failed to load location

---

### 3. Search Locations
**GET** `/api/locations/search?q={query}`

Searches for locations based on a query string.

#### Parameters
- `q` (query): Search query string (required)

#### Response
- **Status Code**: 200 OK
- **Body**:
```json
{
  "locations": [
    {
      "id": 1,
      "name": "Main Office",
      "description": "Primary office location",
      "code": "MAIN001"
    }
  ]
}
```

#### Error Responses
- **400 Bad Request**: Query parameter is required
- **500 Internal Server Error**: Failed to search locations

---

### 4. Create Location
**POST** `/api/locations`

Creates a new location. Requires authentication and audit logging.

#### Request Body
```json
{
  "name": "New Location",
  "description": "Description of the location",
  "code": "LOC001"
}
```

#### Request Fields
- `name` (string, required): Location name
- `description` (string, optional): Location description
- `code` (string, required): Unique location code

#### Response
- **Status Code**: 201 Created
- **Body**:
```json
{
  "message": "location created successfully"
}
```

#### Error Responses
- **400 Bad Request**: Invalid request body
- **401 Unauthorized**: Authentication required
- **500 Internal Server Error**: Failed to create location

---

### 5. Update Location
**PUT** `/api/locations/{id}`

Updates an existing location. Requires authentication and audit logging.

#### Parameters
- `id` (path): Location ID (integer)

#### Request Body
```json
{
  "name": "Updated Location",
  "description": "Updated description",
  "code": "LOC001"
}
```

#### Request Fields
- `name` (string, required): Location name
- `description` (string, optional): Location description
- `code` (string, required): Unique location code

#### Response
- **Status Code**: 200 OK
- **Body**:
```json
{
  "message": "location updated successfully"
}
```

#### Error Responses
- **400 Bad Request**: Invalid location ID or request body
- **401 Unauthorized**: Authentication required
- **500 Internal Server Error**: Failed to update location

---

### 6. Delete Location
**DELETE** `/api/locations/{id}`

Deletes a location by ID. Requires authentication and audit logging.

#### Parameters
- `id` (path): Location ID (integer)

#### Response
- **Status Code**: 200 OK
- **Body**:
```json
{
  "message": "location deleted successfully"
}
```

#### Error Responses
- **400 Bad Request**: Invalid location ID
- **401 Unauthorized**: Authentication required
- **500 Internal Server Error**: Failed to delete location

## Data Models

### LocationResponse
```json
{
  "id": "integer",
  "name": "string",
  "description": "string",
  "code": "string"
}
```

### CreateLocationRequest
```json
{
  "name": "string (required)",
  "description": "string (optional)",
  "code": "string (required)"
}
```

### UpdateLocationRequest
```json
{
  "name": "string (required)",
  "description": "string (optional)",
  "code": "string (required)"
}
```

## Notes
- All timestamps are in UTC
- Location codes must be unique across the system
- Audit logs are automatically created for create, update, and delete operations
- Error responses may include additional `details` field with technical error information