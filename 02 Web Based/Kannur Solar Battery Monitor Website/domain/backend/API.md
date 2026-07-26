# API Reference 📡

This document lists all API endpoints implemented under `/api` with expected request/response shapes and status codes.

> Notes
> - Protected endpoints use JWT (`middleware.JWTAuth`) unless otherwise noted.
> - Device-authenticated endpoints use a device JWT (see `/device-auth/...`).

---

## Authentication 🔐

### POST /api/login
- Body: { "username": string, "password": string }
- Success: 200
  - { "token": string, "user": { "id": number, "name": string, "username": string, "email": string, "role": string } }
- Errors: 400 (invalid request), 401 (invalid credentials), 500 (login failed)

### POST /api/register
- Body: dto.CreateUserRequest: { "username": string (required), "password": string (required), "name"?, "email"?, "role"? }
- Success: 200
  - { "token": string, "user": { "id": number, "name": string, "username": string, "email": string } }
- Errors: 400 (invalid request), 500 (registration/login failed), 401 (invalid credentials)

---

## Devices 🖧

### GET /api/devices
- Auth: none (public)
- Success: 200
  - { "devices": [ ...Device objects... ] }
- Errors: 500

### GET /api/devices/:id
- Success: 200
  - { "device": { ... } }
- Errors: 400 (invalid id), 404 (device not found), 500

### GET /api/devices/:id/readings
- Query: ?limit=50 (default)
- Success: 200
  -` { "latest": <Reading>, "readings": [ ...Reading... ] }`
- Errors: 400, 500

### GET /api/devices/:id/readings/range
- Query: ?start_date=YYYY-MM-DD&end_date=YYYY-MM-DD
- Success: 200
  - { "readings": [...], "stats": { /* aggregated stats */ } }
- Errors: 400 (invalid dates), 500

### GET /api/devices/:id/readings/interval
- Query: ?start_date=YYYY-MM-DD&end_date=YYYY-MM-DD&interval=1h&count=24
- Success: 200
  - { "readings": [...] }
- Errors: 400 (missing/invalid params), 500

### POST /api/devices/:id/control
- Auth: JWT required
- Body: dto.ControlRequest { "action": number }
- Success: 200
  - { "message": { "device": string?, "state": string } }
- Errors: 400 (invalid id), 401 (unauthorized), 404 (device not found), 500 (command failed)

### POST /api/devices
- Auth: JWT required
- Body: dto.CreateDeviceRequest
- Success: 201
  - { "device": { ...created device... } }
- Errors: 400 (validation), 401 (unauthorized), 500

### PUT /api/devices/:id
- Auth: JWT required
- Body: dto.UpdateDeviceRequest (partial fields)
- Success: 200
  - { "message": "device updated successfully" }
- Errors: 400, 401, 500

### PUT /api/devices/:id/full
- Auth: JWT required
- Body: dto.FullUpdateDeviceRequest (all fields required)
- Success: 200
  - { "message": "device fully updated successfully" }
- Errors: 400, 401, 500

### DELETE /api/devices/:id
- Auth: JWT required
- Success: 200
  - { "message": "device deleted successfully" }
- Errors: 400, 401, 500

### GET /api/device/:id/features
- Auth: JWT required
- Success: 200
  - { "features": null } (currently a stub)
- Errors: 400

### GET /api/devices/:id/versions
- Success: 200
  - { "versions": [ ...VersionResponse... ] }
- Errors: 400, 500

### POST /api/devices/:id/versions
- Body: { "previous_version": <uint|null>, "version": string, "features": [ids] }
- Success: 201
  - Version object
- Errors: 400, 500

### GET /api/devices/my
- Auth: JWT required
- Success: 200
  - { "devices": [ ... ] }
- Errors: 401, 500

---

## Device Authentication (device tokens) 🔑

### POST /api/device-auth/token
- Auth: **User** JWT required
- Body: { "device_id": number }
- Success: 200
  - { "token": string, "user_id": number, "device_id": number }
- Errors: 400, 401, 500

### POST /api/device-auth/:device_id/token
- Auth: **User** JWT required
- Success: 200 (same response as above)
- Errors: 400, 401, 500

---

## Readings (device-authenticated) 📈

### POST /api/readings
- Auth: Device JWT (middleware.DeviceJWTAuth)
- Body: dto.EssentialReadingRequest { "voltage": number (required), "current": number }
- Success: 201
  - Reading object as JSON (created reading)
- Errors: 400 (invalid body / missing device_id in context), 500

---

## Device Types 🧩

### GET /api/device-types
- Query: ?limit=&offset=
- Success: 200
  - { "device_types": [ { "id": number, "name": string } ] }
- Errors: 500

### GET /api/device-types/:id
- Success: 200
  - { "device_type": { ... } }
- Errors: 400, 404, 500

---

## Device State & History 🔁

### GET /api/device-states
- Success: 200
  - { "device_states": [ ... ] }
- Errors: 500

### GET /api/device-states/:id
- Success: 200
  - { "device_state": { ... } }
- Errors: 400, 404, 500

### POST /api/device-states
- Auth: JWT required
- Body: dto.CreateDeviceStateRequest { "name": string }
- Success: 201
  - { "message": "device state created successfully" }
- Errors: 400, 500

### PUT /api/device-states/:id
- Auth: JWT required
- Body: dto.UpdateDeviceStateRequest { "name": string }
- Success: 200
  - { "message": "device state updated successfully" }
- Errors: 400, 500

### DELETE /api/device-states/:id
- Auth: JWT required
- Success: 200
  - { "message": "device state deleted successfully" }
- Errors: 400, 500

### GET /api/devices/:id/state-history
- Auth: JWT required
- Query options: ?from_date=YYYY-MM-DD&to_date=YYYY-MM-DD&states=1&states=2
- Success: 200
  - DeviceStateHistoryViewResponse { "history": [...], "total_records": number }
- Errors: 400, 500

---

## Users 👥

### GET /api/users
- Auth: JWT required
- Success: 200
  - { "users": [ ...UserView... ] }
- Errors: 500

### GET /api/users/:id
- Auth: JWT required
- Success: 200
  - { "user": { ... } }
- Errors: 400, 500

### GET /api/profile
- Auth: JWT required
- Success: 200
  - { "profile": dto.UserProfile }
- Errors: 401, 500

### POST /api/users
- Body: dto.CreateUserRequest
- Success: 201
  - { "message": "user created successfully" }
- Errors: 400, 500

### PUT /api/users/:id
- Auth: JWT required
- Body: dto.UpdateUserRequest
- Success: 200
  - { "message": "user updated successfully" }
- Errors: 400, 500

### DELETE /api/users/:id
- Auth: JWT required
- Success: 200
  - { "message": "user deleted successfully" }
- Errors: 400, 500

---

## Audit 🔍

### GET /api/audit
- Auth: JWT required
- Query: ?action=&limit=
- Success: 200
  - { "logs": [ ...AuditLogView {id,username,action,details,ip_address,device_id,created_at} ] }
- Errors: 500

---

## Versions & Features 🧾

### POST /api/versions
- Auth: JWT required
- Body: { "version": string, "previous_version"?: string }
- Success: 201
  - Version object (VersionResponse)
- Errors: 400, 500

### GET /api/versions
- Success: 200
  - { "versions": [ ... ] }
- Errors: 500

### GET /api/versions/:id
- Auth: JWT required
- Success: 200
  - Version object
- Errors: 400, 404, 500

### PUT /api/versions/:id
- Auth: JWT required
- Body: { "version": string }
- Success: 200
  - Version object
- Errors: 400, 500

### DELETE /api/versions/:id
- Auth: JWT required
- Success: 200
  - { "message": "version deleted" }
- Errors: 400, 500

### POST /api/features
- Auth: JWT required
- Body: { "version_id": number, "name": string, "enabled": boolean }
- Success: 201
  - Feature object
- Errors: 400, 500

### GET /api/features/version/:verid
- Auth: JWT required
- Success: 200
  - { "features": [ ... ] }
- Errors: 400, 500

### PUT /api/features/:id
- Auth: JWT required
- Body: { "feature_name": string, "enabled": bool }
- Success: 200
  - Feature object
- Errors: 400, 500

### DELETE /api/features/:id
- Auth: JWT required
- Success: 200
  - { "message": "feature deleted" }
- Errors: 400, 500

---

## Admin ⚙️

### GET /api/admin/stats
- Auth: JWT required
- Success: 200
  - { /* admin stats object returned by AdminService */ }
- Errors: 500

---

## Static routes
- GET / (serves `static/index.html`)
- GET /login (serves `static/login.html`)
- GET /devices/:id (serves `static/device-dashboard.html`)
- GET /devices/:id/readings (serves `static/device.html`)
- GET /all-readings (serves `static/all-readings.html`)
- GET /manage-devices (serves `static/manage-devices.html`)
- GET /manage-users (serves `static/manage-users.html`)
- GET /audit (serves `static/audit.html`)

---

If you'd like, I can:
1. Add example request/response samples for a few endpoints. ✅
2. Generate a small Postman/Insomnia collection based on this spec.

Which would you prefer? 🔧


---


Request

```bash
curl --location --request GET 'localhost:8080/api/devices/types' \
--header 'Content-Type: text/plain' \
--header 'Authorization: ••••••' \
--data '{
    "name": "My Solar Charger",
    "device_type_id": 1,
    "address": "123 Solar Street",
    "city": "Solar City",
    "connected_microcontroller_id": 2
  }'
```

```go
{
    "device_types": [
        {
            "id": 1,
            "name": "esp8266"
        },
        {
            "id": 2,
            "name": "esp32"
        },
        {
            "id": 3,
            "name": "volt-current-meter"
        },
        {
            "id": 4,
            "name": "smart-switch"
        },
        {
            "id": 5,
            "name": "sensor-node"
        },
        {
            "id": 6,
            "name": "temperature-sensor"
        },
        {
            "id": 7,
            "name": "humidity-sensor"
        },
        {
            "id": 8,
            "name": "motion-detector"
        },
        {
            "id": 9,
            "name": "relay-module"
        },
        {
            "id": 10,
            "name": "power-monitor"
        },
        {
            "id": 11,
            "name": "energy-meter"
        }
    ]
}
```

