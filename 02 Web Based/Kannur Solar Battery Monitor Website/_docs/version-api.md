# Version and Feature Management API Documentation

This document describes the API endpoints for managing software versions and features in the SKVMS (Solar Battery Monitoring System).

## Base URL
All endpoints are prefixed with `/api`

## Authentication
- Endpoints marked with 🔐 require JWT authentication via `Authorization: Bearer <token>` header
- Public endpoints have no authentication requirements

---

## Version Endpoints

### 1. Create Version
**POST** `/versions` 🔐

Creates a new software version.

**Request Body:**
```json
{
  "version": "1.2.3",
  "previous_version": "1.2.2"  // optional
}
```

**Response (201 Created):**
```json
{
  "ID": 1,
  "Version": "1.2.3",
  "DeviceID": null,
  "PreviousVersionID": 1,
  "CreatedAt": "2024-01-01T00:00:00Z",
  "UpdatedAt": "2024-01-01T00:00:00Z",
  "Features": [],
  "PreviousVersion": null
}
```

---

### 2. Get All Versions
**GET** `/versions`

Retrieves all software versions in the system.

**Response (200 OK):**
```json
{
  "versions": [
    {
      "ID": 1,
      "Version": "1.0.0",
      "DeviceID": null,
      "PreviousVersionID": null,
      "CreatedAt": "2024-01-01T00:00:00Z",
      "UpdatedAt": "2024-01-01T00:00:00Z",
      "Features": [],
      "PreviousVersion": null
    }
  ]
}
```

---

### 3. Get Version by ID
**GET** `/versions/:id` 🔐

Retrieves a specific version by its ID.

**Path Parameters:**
- `id` (uint): Version ID

**Response (200 OK):**
```json
{
  "ID": 1,
  "Version": "1.0.0",
  "DeviceID": null,
  "PreviousVersionID": null,
  "CreatedAt": "2024-01-01T00:00:00Z",
  "UpdatedAt": "2024-01-01T00:00:00Z",
  "Features": [],
  "PreviousVersion": null
}
```

**Error Responses:**
- `400 Bad Request`: Invalid version ID
- `404 Not Found`: Version not found

---

### 4. Update Version
**PUT** `/versions/:id` 🔐

Updates an existing version's information.

**Path Parameters:**
- `id` (uint): Version ID

**Request Body:**
```json
{
  "version": "1.0.1"
}
```

**Response (200 OK):**
```json
{
  "ID": 1,
  "Version": "1.0.1",
  "DeviceID": null,
  "PreviousVersionID": null,
  "CreatedAt": "2024-01-01T00:00:00Z",
  "UpdatedAt": "2024-01-01T00:00:01Z",
  "Features": [],
  "PreviousVersion": null
}
```

**Error Responses:**
- `400 Bad Request`: Invalid version ID or request body
- `500 Internal Server Error`: Update failed

---

### 5. Delete Version
**DELETE** `/versions/:id` 🔐

Deletes a version from the system.

**Path Parameters:**
- `id` (uint): Version ID

**Response (200 OK):**
```json
{
  "message": "version deleted"
}
```

**Error Responses:**
- `400 Bad Request`: Invalid version ID
- `500 Internal Server Error`: Deletion failed

---

## Feature Endpoints

### 6. Create Feature
**POST** `/features` 🔐

Creates a new feature and associates it with a version.

**Request Body:**
```json
{
  "version_id": 1,
  "name": "Battery Monitoring",
  "enabled": true
}
```

**Response (201 Created):**
```json
{
  "ID": 1,
  "FeatureName": "Battery Monitoring",
  "Enabled": true,
  "Versions": []
}
```

---

### 7. Get Features by Version
**GET** `/features/version/:verid` 🔐

Retrieves all features associated with a specific version.

**Path Parameters:**
- `verid` (uint): Version ID

**Response (200 OK):**
```json
{
  "features": [
    {
      "ID": 1,
      "FeatureName": "Battery Monitoring",
      "Enabled": true,
      "Versions": []
    }
  ]
}
```

**Error Responses:**
- `400 Bad Request`: Invalid version ID
- `500 Internal Server Error`: Failed to retrieve features

---

### 8. Update Feature
**PUT** `/features/:id` 🔐

Updates an existing feature's information.

**Path Parameters:**
- `id` (uint): Feature ID

**Request Body:**
```json
{
  "feature_name": "Advanced Battery Monitoring",
  "enabled": false
}
```

**Response (200 OK):**
```json
{
  "ID": 1,
  "FeatureName": "Advanced Battery Monitoring",
  "Enabled": false,
  "Versions": []
}
```

**Error Responses:**
- `400 Bad Request`: Invalid feature ID or request body
- `500 Internal Server Error`: Update failed

---

### 9. Delete Feature
**DELETE** `/features/:id` 🔐

Deletes a feature from the system.

**Path Parameters:**
- `id` (uint): Feature ID

**Response (200 OK):**
```json
{
  "message": "feature deleted"
}
```

**Error Responses:**
- `400 Bad Request`: Invalid feature ID
- `500 Internal Server Error`: Deletion failed

---

## Data Models

### Version
```go
type Version struct {
    ID                uint      `json:"ID"`
    Name              string    `json:"Version"`
    DeviceID          uint      `json:"DeviceID,omitempty"`
    PreviousVersionID *uint     `json:"PreviousVersionID,omitempty"`
    CreatedAt         time.Time `json:"CreatedAt"`
    UpdatedAt         time.Time `json:"UpdatedAt"`
    Features          []Feature `json:"Features"`
    PreviousVersion   *Version  `json:"PreviousVersion,omitempty"`
}
```

### Feature
```go
type Feature struct {
    ID          uint      `json:"ID"`
    FeatureName string    `json:"FeatureName"`
    Enabled     bool      `json:"Enabled"`
    Versions    []Version `json:"Versions"`
}
```

## Notes
- Versions can be associated with devices for tracking device-specific software versions
- Features are backwards compatible and can be associated with multiple versions
- The system maintains version history through the `PreviousVersionID` relationship
- All create/update operations require proper authentication</content>
<parameter name="filePath">/home/aruncs/Startups/Sooraj_Sirs/skvms/docs/version-api.md