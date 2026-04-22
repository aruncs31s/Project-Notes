# API Routes

All API endpoints are prefixed with `/api`.

---

## Authentication

| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | `/login` | User login |
| POST | `/register` | User registration |
| POST | `/refresh` | Refresh JWT token |

---

## Users

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/users` | List all users |
| GET | `/users/:id` | Get user by ID |
| GET | `/users/:id/profile` | Get user profile |
| GET | `/profile` | Get current user profile |
| POST | `/users` | Create user |
| PUT | `/users/:id` | Update user |
| DELETE | `/users/:id` | Delete user |

---

## Devices

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/devices` | List all devices |
| GET | `/devices/recent` | List recent devices |
| GET | `/devices/:id` | Get device by ID |
| GET | `/devices/my` | Get my devices |
| GET | `/devices/my/stats` | Get my device stats |
| GET | `/devices/search` | Search devices |
| GET | `/devices/types` | List device types |
| GET | `/devices/types/hardware` | List hardware types |
| GET | `/devices/types/sensors` | List sensor types |
| GET | `/devices/states` | List device states |
| GET | `/devices/states/:id` | Get device state |
| POST | `/devices` | Create device |
| POST | `/devices/types` | Create device type |
| POST | `/devices/:id/control` | Control device |
| PUT | `/devices/:id` | Update device |
| PUT | `/devices/:id/full` | Full update device |
| PUT | `/devices/states/:id` | Update device state |
| DELETE | `/devices/:id` | Delete device |

---

## Device Types

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/device-types` | List device types |
| GET | `/devices/:id/type` | Get device type by device ID |

---

## Sensors

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/sensors` | List all sensors |
| GET | `/sensors/:id` | Get sensor by ID |
| GET | `/sensors/:id/connected` | Get sensor's connected devices |
| GET | `/sensors/search` | Search sensors |
| POST | `/sensors` | Create sensor |

---

## Solar Devices

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/solar` | List all solar devices |
| GET | `/solar/my` | Get my solar devices |
| GET | `/solar/count` | Get solar device count |
| GET | `/solar/offline` | Get offline solar devices |
| POST | `/solar` | Create solar device |

---

## Microcontrollers

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/devices/microcontrollers` | List microcontroller devices |
| GET | `/devices/microcontrollers/stats` | Get microcontroller stats |
| GET | `/devices/microcontrollers/my` | Get my microcontrollers |
| GET | `/devices/microcontrollers/my/stats` | Get my microcontroller stats |

---

## Readings

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/devices/:id/readings` | List readings for device |
| GET | `/devices/:id/readings/range` | Get readings by date range |
| GET | `/devices/:id/readings/interval` | Get readings with interval |
| GET | `/devices/:id/readings/progressive` | Get progressive readings |
| GET | `/devices/:id/connected/:cid/readings` | Get readings of connected device |
| GET | `/readings` | Advanced readings (with filters) |
| GET | `/locations/:id/readings/seven` | 7-day readings for location |
| POST | `/readings` | Create reading (device auth) |

### Query Parameters for `/readings`

| Parameter | Type | Description |
|-----------|------|-------------|
| `device_id` | uint | Filter by device ID |
| `location_id` | uint | Filter by location ID |
| `start_time` | datetime | Start time filter (ISO 8601) |
| `end_time` | datetime | End time filter (ISO 8601) |
| `limit` | int | Number of results (default 100, max 5000) |
| `offset` | int | Pagination offset |

### Query Parameters for `/devices/:id/readings/range`

| Parameter | Type | Description |
|-----------|------|-------------|
| `start_date` | date | Start date (YYYY-MM-DD) |
| `end_date` | date | End date (YYYY-MM-DD) |

---

## Locations

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/locations` | List all locations |
| GET | `/locations/:id` | Get location by ID |
| GET | `/locations/search` | Search locations |
| GET | `/locations/:id/devices` | List devices in location |
| POST | `/locations` | Create location |
| PUT | `/locations/:id` | Update location |
| DELETE | `/locations/:id` | Delete location |

---

## Device Tokens

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/tokens` | Get my tokens |
| GET | `/devices/:id/tokens` | Get device tokens |
| POST | `/device-auth/token` | Generate device token |
| POST | `/devices/:id/token` | Generate token for device |
| DELETE | `/tokens/:token_id` | Revoke token |

---

## Ownership

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/devices/:id/ownership` | Get device ownership |
| POST | `/devices/:id/transfer` | Transfer ownership |
| GET | `/devices/:id/transfer-history` | Get transfer history |
| PUT | `/devices/:id/public` | Set device public |

---

## Notifications

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/notifications` | Get notifications |
| PUT | `/notifications/read-all` | Mark all as read |
| PUT | `/notifications/:id/read` | Mark as read |

---

## Device State History

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/devices/:id/states/history` | Get device state history |
| POST | `/devices/states` | Create device state |
| GET | `/devices/states/:id` | Get device state |

---

## Versions & Features

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/versions` | List all versions |
| GET | `/versions/:id` | Get version by ID |
| GET | `/features/version/:verid` | Get features by version |
| POST | `/versions` | Create version |
| POST | `/features` | Create feature |
| PUT | `/versions/:id` | Update version |
| PUT | `/features/:id` | Update feature |
| DELETE | `/versions/:id` | Delete version |
| DELETE | `/features/:id` | Delete feature |

---

## Audit Logs

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/audit` | List audit logs |

---

## Code Generation

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/codegen/tools` | List available tools |
| POST | `/codegen/generate` | Generate code |
| POST | `/codegen/build` | Build code |
| POST | `/codegen/build-and-download` | Build and download |
| GET | `/codegen/download/:build_id` | Download build |
| POST | `/codegen/upload` | Upload file |
| DELETE | `/codegen/builds/:build_id` | Cleanup build |

---

## Export

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/export/formats` | List export formats |
| GET | `/export/readings` | Export readings |

---

## Admin

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/admin/stats` | Get admin stats |