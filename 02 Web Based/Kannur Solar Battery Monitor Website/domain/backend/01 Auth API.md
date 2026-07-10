# SKVMS API Documentation: Comprehensive Route Guide

Last updated: 2026-04-09
Base path: `/api`

## Table of Contents
1. [Authentication](#authentication)
2. [User Management](#user-management)
3. [Device Routes](#device-routes)
4. [Connected Devices](#connected-devices)
5. [Device Reads & Analytics](#device-readings--analytics)
6. [Solar Systems](#solar-systems)
7. [Device States & History](#device-states--history)
8. [Locations](#locations)
9. [Versions & Features](#versions--features)
10. [Firmware Codegen](#firmware-codegen)
11. [Data Export](#data-export)
12. [Audit & Monitoring](#audit--monitoring)
13. [Device Ownership & Privacy](#device-ownership--privacy)

---

## Authentication

### Login
- **Method:** `POST`
- **Path:** `/api/login`
- **Payload:**
  ```json
  {
    "username": "admin",
    "password": "password123"
  }
  ```
- **Response (200):**
  ```json
  {
    "token": "<jwt_access_token>",
    "user": { ...user_details }
  }
  ```

### Register
- **Method:** `POST`
- **Path:** `/api/register`
- **Payload:**
  ```json
  {
    "name": "Arun CS",
    "username": "aruncs",
    "email": "arun@example.com",
    "password": "secure_password",
    "role": "admin"
  }
  ```

### Refresh Token
- **Method:** `POST`
- **Path:** `/api/refresh`
- **Payload:**
  ```json
  {
    "refresh_token": "<token>"
  }
  ```

---

## User Management

### Get My Profile
- **Method:** `GET`
- **Path:** `/api/profile`
- **Auth:** Required (JWT)
- **Response:** Returns user information, owned devices, and recent activity (audit logs).

### List Users
- **Method:** `GET`
- **Path:** `/api/users`
- **Auth:** Required (Admin)

### Create/Update/Delete User
- **Methods:** `POST`, `PUT /:id`, `DELETE /:id`
- **Path:** `/api/users`

---

## Device Routes

### List All Devices
- **Method:** `GET`
- **Path:** `/api/devices`
- **Query Params:** `limit` (optional)

### Get Device Details
- **Method:** `GET`
- **Path:** `/api/devices/:id`

### Create Device
- **Method:** `POST`
- **Path:** `/api/devices`
- **Auth:** Required
- **Payload:**
  ```json
  {
    "name": "Solar Battery unit 1",
    "type": 1,
    "ip_address": "192.168.1.10",
    "mac_address": "AA:BB:CC:DD:EE:FF",
    "firmware_version_id": 1
  }
  ```

### Update Device
- **Method:** `PUT`
- **Path:** `/api/devices/:id`
- **Payload:** Partial update (optional fields: name, type, ip, mac, version_id).

### Full Update Device
- **Method:** `PUT`
- **Path:** `/api/devices/:id/full`
- **Payload:** Requires all fields including `current_state`.

### Control Device
- **Method:** `POST`
- **Path:** `/api/devices/:id/control`
- **Payload:** `{"action": 1}`

### Search Devices
- **Path:** `/api/devices/search?q=term`
- **Specialized search:** 
  - `/api/devices/search/microcontrollers?q=term`
  - `/api/devices/search/sensors?q=term`

---

## Connected Devices
Manage child devices (controllers/sensors) linked to a parent device.

### List Connected
- **Method:** `GET`
- **Path:** `/api/devices/:id/connected`

### Add Existing Device as Child
- **Method:** `POST`
- **Path:** `/api/devices/:id/connected`
- **Payload:** `{"child_id": 101}`

### Create New Connected Device
- **Method:** `POST`
- **Path:** `/api/devices/:id/connected/new`
- **Payload:** Full device details (name, type, ip, mac).

### Remove Connection
- **Method:** `DELETE`
- **Path:** `/api/devices/:id/connected/:cid`

---

## Device Readings & Analytics

### Recent Readings
- **Method:** `GET`
- **Path:** `/api/devices/:id/readings`
- **Query Params:** `limit` (default 50)

### Readings by Date Range
- **Method:** `GET`
- **Path:** `/api/devices/:id/readings/range`
- **Query Params:** `start_date`, `end_date` (Format: YYYY-MM-DD)

### Progressive Readings
- **Method:** `GET`
- **Path:** `/api/devices/:id/readings/progressive`
- **Usage:** Optimized for real-time charting and historical progression.

### Readings with Interval
- **Method:** `GET`
- **Path:** `/api/devices/:id/readings/interval`
- **Query Params:** `start_date`, `end_date`, `interval` (e.g. `1h`, `15m`), `count`.

### Child Device Readings
- **Method:** `GET`
- **Path:** `/api/devices/:id/connected/:cid/readings`
- **Usage:** Fetch readings specifically for a sub-sensor connected to a controller.

---

## Solar Systems

### List Solar Units
- **Path:** `/api/devices/solar`
- **Path:** `/api/devices/solar/my` (user specific)

### Solar Stats
- **Path:** `/api/devices/solar/count` (Total units)
- **Path:** `/api/devices/solar/offline` (Offline units)

---

## Device States & History

### Manage States
- **List All:** `GET /api/devices/states`
- **Create:** `POST /api/devices/states`
- **Update:** `PUT /api/devices/states/:id`

### State Change History
- **Method:** `GET`
- **Path:** `/api/devices/:id/states/history`
- **Usage:** View when a device switched from online/offline or other custom states.

---

## Locations

### Geographic Management
- **Routes:** `GET`, `POST`, `PUT`, `DELETE` at `/api/locations`
- **Search:** `GET /api/locations/search?q=<term>`
- **Devices in Location:** `GET /api/locations/:id/devices`
- **7-Day Location Readings:** `GET /api/locations/:id/readings/seven`

---

## Versions & Features

### Firmware Versions
- **Routes:** `GET`, `POST`, `PUT`, `DELETE` at `/api/versions`
- **Device Specific Versions:** `GET /api/devices/:id/versions`

### Version Features
- **Routes:** `GET`, `POST`, `PUT`, `DELETE` at `/api/features`
- **By Version:** `GET /api/features/version/:verid`
- **By Device:** `GET /api/devices/:id/features`

---

## Firmware Codegen
ESP32 Firmware generation and deployment.

### Tools & Generation
- **List Build Tools:** `GET /api/codegen/tools`
- **Request Generation:** `POST /api/codegen/generate`
- **Build Firmware:** `POST /api/codegen/build`
- **Build & Download:** `POST /api/codegen/build-and-download`
- **Download (Binary):** `GET /api/codegen/download/:build_id`

---

## Data Export

### Formats & Reports
- **List Formats:** `GET /api/export/formats` (PDF, XLSX, CSV, XML)
- **Export Readings:** `GET /api/export/readings`
  - *Query Params:* `format`, `device_id`, `start_date`, `end_date`, `template`
- **Export Devices:** `GET /api/export/devices`

---

## Audit & Monitoring

### Activity Logs
- **Method:** `GET`
- **Path:** `/api/audit`
- **Usage:** Tracking user actions like device creation, updates, and controls.

### Admin Dashboard Stats
- **Method:** `GET`
- **Path:** `/api/admin/stats`
- **Response:** Aggregate counts of users, devices, readings, and active/inactive status.

---

## Device Ownership & Privacy
Manage device ownership, transfers (switching), and visibility settings.

### Get Ownership Info
- **Method:** `GET`
- **Path:** `/api/devices/:id/ownership`
- **Auth:** Required
- **Response:** Returns current owner and visibility status.

### Transfer Ownership (Switch Owner)
- **Method:** `POST`
- **Path:** `/api/devices/:id/transfer`
- **Auth:** Required (Admin or Current Owner)
- **Payload:**
  ```json
  {
    "to_user_id": 3,
    "note": "Optional handover note"
  }
  ```
- **Description:** Transfers full ownership to another user. Revokes old permissions and grants them to the new owner.

### Set Device Visibility
- **Method:** `PUT`
- **Path:** `/api/devices/:id/public`
- **Auth:** Required (Admin or Current Owner)
- **Payload:**
  ```json
  {
    "is_public": true
  }
  ```
- **Description:** Toggles whether the device is public (visible to all) or private (owner/admin only).

### Ownership Transfer History
- **Method:** `GET`
- **Path:** `/api/devices/:id/transfer-history`
- **Auth:** Required
- **Usage:** Retrieve the full history of ownership changes for a specific device.