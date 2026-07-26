# SKVMS API Reference (Route Inventory)

This document lists **all HTTP routes registered in the backend router** and documents them **one by one**.

- Router sources:
  - `internal/router/router.go`
  - `internal/router/solar_routes.go`
- API base path: `/api`

## Authentication

### JWT user auth (recommended)
Most protected endpoints require a user JWT:
- Header: `Authorization: Bearer <jwt>`

In code this is applied via `middleware.JWTAuth(r.jwtSecret)`.

### Device JWT auth (for device telemetry)
`POST /api/readings` requires a **device JWT**, applied via `middleware.DeviceJWTAuth(...)`.

---

## Static / HTML Routes

These routes serve static HTML pages (legacy UI) and are not under `/api`.

### GET /static/*
- Auth: None
- Handler: Gin static file server (`router.Static("/static", "./static")`)
- Purpose: Serves static assets.

### GET /
- Auth: None
- Purpose: Serves `./static/index.html`.

### GET /login
- Auth: None
- Purpose: Serves `./static/login.html`.

### GET /devices/:id
- Auth: None
- Path params: `id`
- Purpose: Serves `./static/device-dashboard.html`.

### GET /devices/:id/readings
- Auth: None
- Path params: `id`
- Purpose: Serves `./static/device.html`.

### GET /all-readings
- Auth: None
- Purpose: Serves `./static/all-readings.html`.

### GET /manage-devices
- Auth: None
- Purpose: Serves `./static/manage-devices.html`.

### GET /manage-users
- Auth: None
- Purpose: Serves `./static/manage-users.html`.

### GET /audit
- Auth: None
- Purpose: Serves `./static/audit.html`.

---

## Auth API

### POST /api/login
- Auth: None
- Handler: `AuthHandler.Login`
- Body (JSON):
  - `{ "username": string, "password": string }`
- Response: `{ token, user }` on success.

### POST /api/register
- Auth: None
- Handler: `AuthHandler.Register`
- Body (JSON): `dto.CreateUserRequest` (see `internal/dto/user.go`)
- Response: `{ token, user }` on success.

---

## Devices API

### GET /api/devices
- Auth: None
- Handler: `DeviceHandler.ListDevices`
- Response: `{ "devices": [...] }`

### GET /api/devices/:id
- Auth: None
- Handler: `DeviceHandler.GetDevice`
- Path params: `id`
- Response: `{ "device": {...} }`

### POST /api/devices
- Auth: JWT
- Handler: `DeviceHandler.CreateDevice`
- Body (JSON): `dto.CreateDeviceRequest` (see `internal/dto/device.go`)
- Response: `{ "device": {...} }`

### PUT /api/devices/:id
- Auth: JWT (+ audit middleware `device_update`)
- Handler: `DeviceHandler.UpdateDevice`
- Path params: `id`
- Body (JSON): `dto.UpdateDeviceRequest` (see `internal/dto/device.go`)
- Response: `{ "message": "device updated successfully" }`

### PUT /api/devices/:id/full
- Auth: JWT (+ audit middleware `device_full_update`)
- Handler: `DeviceHandler.FullUpdateDevice`
- Path params: `id`
- Body (JSON): `dto.FullUpdateDeviceRequest` (see `internal/dto/device.go`)
- Response: `{ "message": "device fully updated successfully" }`

### DELETE /api/devices/:id
- Auth: JWT
- Handler: `DeviceHandler.DeleteDevice`
- Path params: `id`
- Response: `{ "message": "device deleted successfully" }`

### POST /api/devices/:id/control
- Auth: JWT
- Handler: `DeviceHandler.ControlDevice`
- Path params: `id`
- Body (JSON): `dto.ControlRequest` (see `internal/dto/control.go`)
- Response: `{ "message": {...} }`

### GET /api/devices/my
- Auth: JWT
- Handler: `DeviceHandler.GetMyDevices`
- Response: `{ "devices": [...] }`

---

## Device Types API

These routes are split across two route groups in the router.

### GET /api/devices/types
- Auth: None
- Handler: `DeviceTypesHandler.ListDeviceTypes`
- Notes: Detailed documentation available in `docs/device-types-api.md`.

### POST /api/devices/types
- Auth: JWT
- Handler: `DeviceTypesHandler.CreateDeviceType`
- Notes: Detailed documentation available in `docs/device-types-api.md`.

### GET /api/devices/types/hardware
- Auth: JWT
- Handler: `DeviceTypesHandler.GetHardwareType`
- Notes: Detailed documentation available in `docs/device-types-api.md`.

### GET /api/devices/types/sensors
- Auth: None
- Handler: `DeviceTypesHandler.GetSensorType`

### GET /api/devices/:id/type
- Auth: None
- Handler: `DeviceTypesHandler.GetDeviceTypeByDeviceID`
- Path params: `id`

### GET /api/device-types
- Auth: None
- Handler: `DeviceTypesHandler.ListDeviceTypes`
- Notes: This is a second (legacy/alternate) listing route.

---

## Connected Devices API

### GET /api/devices/:id/connected
- Auth: None
- Handler: `DeviceHandler.GetConnectedDevices`
- Path params: `id` (parent device id)

### POST /api/devices/:id/connected
- Auth: JWT
- Handler: `DeviceHandler.CreateConnectedDevice`
- Path params: `id` (parent device id)
- Body (JSON): Typically a connected-device create request (see `dto.CreateConnectedDeviceRequest` in `internal/dto/device.go`).

### POST /api/devices/:id/connected/new
- Auth: JWT
- Handler: `DeviceHandler.CreateConnectedDeviceWithDetails`
- Path params: `id`

### DELETE /api/devices/:id/connected/:cid
- Auth: JWT
- Handler: `DeviceHandler.RemoveConnectedDevice`
- Path params: `id` (parent id), `cid` (connected device id)

### GET /api/devices/:id/connected/:cid/readings
- Auth: None
- Handler: `ReadingHandler.GetReadingsOfConnectedDevice`
- Path params: `id`, `cid`
- Query params:
  - `start_date` (optional, `YYYY-MM-DD`)
  - `end_date` (optional, `YYYY-MM-DD`)

---

## Readings API

### GET /api/devices/:id/readings
- Auth: None
- Handler: `ReadingHandler.ListByDevice`
- Path params: `id`
- Query params:
  - `limit` (optional, int; default 50)

### GET /api/devices/:id/readings/range
- Auth: None
- Handler: `ReadingHandler.ListByDateRange`
- Path params: `id`
- Query params:
  - `start_date` (required, `YYYY-MM-DD`)
  - `end_date` (required, `YYYY-MM-DD`)

### GET /api/devices/:id/readings/progressive
- Auth: None
- Handler: `ReadingHandler.ListByDeviceProgressive`
- Path params: `id`

### GET /api/devices/:id/readings/interval
- Auth: None
- Handler: `ReadingHandler.ListByDeviceWithInterval`
- Path params: `id`
- Query params:
  - `start_date` (required, `YYYY-MM-DD`)
  - `end_date` (required, `YYYY-MM-DD`)
  - `interval` (optional, Go duration string like `"30m"`, `"1h"`; default `1h`)
  - `count` (optional, int; default 24)

### POST /api/readings
- Auth: Device JWT
- Handler: `ReadingHandler.CreateReading` (implemented in `internal/handler/http/reading_writer.go`)
- Body (JSON): `dto.EssentialReadingRequest` (see `internal/dto/readings.go`)
  - `{ "voltage": number, "current": number }`
- Response: 201 with the stored reading.

---

## Device State API

### GET /api/devices/states
- Auth: None
- Handler: `DeviceStateHandler.ListDeviceStates`

### GET /api/devices/states/:id
- Auth: None
- Handler: `DeviceStateHandler.GetDeviceState`
- Path params: `id`

### POST /api/devices/states
- Auth: JWT
- Handler: `DeviceStateHandler.CreateDeviceState`
- Body (JSON): `dto.CreateDeviceStateRequest` (see `internal/dto/device.go`)

### PUT /api/devices/states/:id
- Auth: JWT
- Handler: `DeviceStateHandler.UpdateDeviceState`
- Path params: `id`
- Body (JSON): `dto.UpdateDeviceStateRequest` (see `internal/dto/device.go`)

### GET /api/devices/:id/states/history
- Auth: JWT
- Handler: `DeviceStateHandler.GetDeviceStateHistory`
- Path params: `id`

---

## Users API

### GET /api/users
- Auth: JWT
- Handler: `UserHandler.ListUsers`

### GET /api/users/:id
- Auth: JWT
- Handler: `UserHandler.GetUser`
- Path params: `id`

### GET /api/profile
- Auth: JWT
- Handler: `UserHandler.GetProfile`

### POST /api/users
- Auth: None
- Handler: `UserHandler.CreateUser`
- Body (JSON): `dto.CreateUserRequest` (see `internal/dto/user.go`)

### PUT /api/users/:id
- Auth: JWT
- Handler: `UserHandler.UpdateUser`
- Path params: `id`
- Body (JSON): `dto.UpdateUserRequest` (see `internal/dto/user.go`)

### DELETE /api/users/:id
- Auth: JWT
- Handler: `UserHandler.DeleteUser`
- Path params: `id`

---

## Audit API

### GET /api/audit
- Auth: JWT
- Handler: `AuditHandler.ListAuditLogs`

---

## Admin API

### GET /api/admin/stats
- Auth: JWT
- Handler: `AdminHandler.GetStats`

---

## Versions & Features API

Notes: Detailed documentation for these endpoints is available in `docs/version-api.md`.

### POST /api/versions
- Auth: JWT
- Handler: `VersionHandler.CreateVersion`

### GET /api/versions
- Auth: None
- Handler: `VersionHandler.GetAllVersions`

### GET /api/versions/:id
- Auth: JWT
- Handler: `VersionHandler.GetVersion`
- Path params: `id`

### PUT /api/versions/:id
- Auth: JWT
- Handler: `VersionHandler.UpdateVersion`
- Path params: `id`

### DELETE /api/versions/:id
- Auth: JWT
- Handler: `VersionHandler.DeleteVersion`
- Path params: `id`

### POST /api/features
- Auth: JWT
- Handler: `VersionHandler.CreateFeature`

### GET /api/features/version/:verid
- Auth: JWT
- Handler: `VersionHandler.GetFeaturesByVersion`
- Path params: `verid`

### PUT /api/features/:id
- Auth: JWT
- Handler: `VersionHandler.UpdateFeature`
- Path params: `id`

### DELETE /api/features/:id
- Auth: JWT
- Handler: `VersionHandler.DeleteFeature`
- Path params: `id`

### GET /api/device/:id/features
- Auth: JWT
- Handler: `VersionHandler.GetAllFeaturesByDevice`
- Path params: `id`
- Notes: Currently returns `{ "features": null }` in handler implementation.

### GET /api/devices/:id/versions
- Auth: None
- Handler: `VersionHandler.GetVersionsByDevice`
- Path params: `id`

### POST /api/devices/:id/versions
- Auth: None
- Handler: `VersionHandler.CreateNewDeviceVersion`
- Path params: `id`

---

## Sensors API

Base group: `/api/devices/sensors`

### GET /api/devices/sensors
- Auth: None
- Handler: `DeviceHandler.ListAllSensors`

### POST /api/devices/sensors
- Auth: None
- Handler: `DeviceHandler.CreateSensorDevice`

### GET /api/devices/sensors/:id
- Auth: None
- Handler: `DeviceHandler.GetSensorDevice`
- Path params: `id`

### GET /api/devices/sensors/:id/connected
- Auth: None
- Handler: `DeviceHandler.GetConnectedDevices`
- Path params: `id`

### GET /api/devices/sensors/search
- Auth: None
- Handler: `DeviceHandler.SearchSensorDevices`

---

## Solar Devices API

Base group: `/api/devices/solar`

### GET /api/devices/solar
- Auth: JWT
- Handler: `SolarHandler.GetAllSolarDevices`

### GET /api/devices/solar/my
- Auth: JWT
- Handler: `SolarHandler.GetAllMySolarDevices`

### POST /api/devices/solar
- Auth: JWT
- Handler: `SolarHandler.CreateASolarDevice`

### GET /api/devices/solar/count
- Auth: JWT
- Handler: `DeviceHandler.GetTotalCount`

### GET /api/devices/solar/offline
- Auth: JWT
- Handler: `DeviceHandler.GetOfflineDevices`

---

## Device Search API

### GET /api/devices/search
- Auth: None
- Handler: `DeviceHandler.SearchDevices`

### GET /api/devices/search/microcontrollers
- Auth: None
- Handler: `DeviceHandler.SearchMicrocontollerDevices`

### GET /api/devices/search/sensors
- Auth: None
- Handler: `DeviceHandler.SearchSensorDevices`

### GET /api/devices/microcontrollers
- Auth: None
- Handler: `DeviceHandler.ListMicrocontrollerDevices`

---

## Device Auth (Token) API

### POST /api/device-auth/token
- Auth: JWT
- Handler: `DeviceAuthHandler.GenerateDeviceToken`
- Body (JSON): `{ "device_id": number }`
- Response: `{ "token": "...", "user_id": number, "device_id": number }`

---

## ESP32 Firmware CodeGen API

Notes: Detailed documentation is available in `docs/esp32-codegen-api.md`.

Base group: `/api/codegen`

### GET /api/codegen/tools
- Auth: None
- Handler: `CodeGenHandler.ListTools`

### POST /api/codegen/generate
- Auth: JWT
- Handler: `CodeGenHandler.Generate`

### POST /api/codegen/build
- Auth: JWT
- Handler: `CodeGenHandler.Build`
- Response includes `download_url`.

### POST /api/codegen/build-and-download
- Auth: JWT
- Handler: `CodeGenHandler.GenerateAndDownload`
- Response is the binary file.

### GET /api/codegen/download/:build_id
- Auth: JWT
- Handler: `CodeGenHandler.Download`
- Path params: `build_id`

### POST /api/codegen/upload
- Auth: JWT
- Handler: `CodeGenHandler.Upload`
- Body: `CodeGenRequest + device_ip`

### DELETE /api/codegen/builds/:build_id
- Auth: JWT
- Handler: `CodeGenHandler.Cleanup`
- Path params: `build_id`

---

## Notes / Known Oddities

- There are two device-type listing routes: `/api/devices/types` and `/api/device-types`.
- Some routes are public that you may want to protect (example: `POST /api/users`, `POST /api/devices/:id/versions`). This document reflects current router behavior.
