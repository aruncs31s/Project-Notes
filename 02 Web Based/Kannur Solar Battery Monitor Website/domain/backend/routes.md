# API Routes

> All API endpoints are prefixed with `/api`.

---

## Authentication

| Endpoint | Method | Description |
|----------|:------:|-------------|
| `/login` | POST | User login |
| `/register` | POST | User registration |
| `/refresh` | POST | Refresh JWT token |

---

## Users

| Endpoint | Method | Description |
|----------|:------:|-------------|
| `/users` | GET | List all users |
| `/users/:id` | GET | Get user by ID |
| `/users/:id/profile` | GET | Get user profile |
| `/profile` | GET | Get current user profile |
| `/users` | POST | Create user |
| `/users/:id` | PUT | Update user |
| `/users/:id` | DELETE | Delete user |

---

## Devices

| Endpoint | Method | Description |
|----------|:------:|-------------|
| `/devices` | GET | List all devices |
| `/devices/recent` | GET | List recent devices |
| `/devices/:id` | GET | Get device by ID |
| `/devices/my` | GET | Get my devices |
| `/devices/my/stats` | GET | Get my device stats |
| `/devices/search` | GET | Search devices |
| `/devices/types` | GET | List device types |
| `/devices/types/hardware` | GET | List hardware types |
| `/devices/types/sensors` | GET | List sensor types |
| `/devices/states` | GET | List device states |
| `/devices/states/:id` | GET | Get device state |
| `/devices` | POST | Create device |
| `/devices/types` | POST | Create device type |
| `/devices/:id/control` | POST | Control device |
| `/devices/:id` | PUT | Update device |
| `/devices/:id/full` | PUT | Full update device |
| `/devices/states/:id` | PUT | Update device state |
| `/devices/:id` | DELETE | Delete device |

---

## Device Types

| Endpoint | Method | Description |
|----------|:------:|-------------|
| `/device-types` | GET | List device types |
| `/devices/:id/type` | GET | Get device type by device ID |

---

## Sensors

| Endpoint | Method | Description |
|----------|:------:|-------------|
| `/sensors` | GET | List all sensors |
| `/sensors/:id` | GET | Get sensor by ID |
| `/sensors/:id/connected` | GET | Get sensor's connected devices |
| `/sensors/search` | GET | Search sensors |
| `/sensors` | POST | Create sensor |

---

## Solar Devices

| Endpoint | Method | Description |
|----------|:------:|-------------|
| `/solar` | GET | List all solar devices |
| `/solar/my` | GET | Get my solar devices |
| `/solar/count` | GET | Get solar device count |
| `/solar/offline` | GET | Get offline solar devices |
| `/solar` | POST | Create solar device |

---

## Microcontrollers

| Endpoint | Method | Description |
|----------|:------:|-------------|
| `/devices/microcontrollers` | GET | List microcontroller devices |
| `/devices/microcontrollers/stats` | GET | Get microcontroller stats |
| `/devices/microcontrollers/my` | GET | Get my microcontrollers |
| `/devices/microcontrollers/my/stats` | GET | Get my microcontroller stats |

---

## Readings

| Endpoint | Method | Description |
|----------|:------:|-------------|
| `/devices/:id/readings` | GET | List readings for device |
| `/devices/:id/readings/range` | GET | Get readings by date range |
| `/devices/:id/readings/interval` | GET | Get readings with interval |
| `/devices/:id/readings/progressive` | GET | Get progressive readings |
| `/devices/:id/connected/:cid/readings` | GET | Get readings of connected device |
| `/readings` | GET | Advanced readings (with filters) |
| `/locations/:id/readings/seven` | GET | 7-day readings for location |
| `/readings` | POST | Create reading (device auth) |

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

| Endpoint | Method | Description |
|----------|:------:|-------------|
| `/locations` | GET | List all locations |
| `/locations/:id` | GET | Get location by ID |
| `/locations/search` | GET | Search locations |
| `/locations/:id/devices` | GET | List devices in location |
| `/locations` | POST | Create location |
| `/locations/:id` | PUT | Update location |
| `/locations/:id` | DELETE | Delete location |

---

## Device Tokens

| Endpoint | Method | Description |
|----------|:------:|-------------|
| `/tokens` | GET | Get my tokens |
| `/devices/:id/tokens` | GET | Get device tokens |
| `/device-auth/token` | POST | Generate device token |
| `/devices/:id/token` | POST | Generate token for device |
| `/tokens/:token_id` | DELETE | Revoke token |

---

## Ownership

| Endpoint | Method | Description |
|----------|:------:|-------------|
| `/devices/:id/ownership` | GET | Get device ownership |
| `/devices/:id/transfer` | POST | Transfer ownership |
| `/devices/:id/transfer-history` | GET | Get transfer history |
| `/devices/:id/public` | PUT | Set device public |

---

## Notifications

| Endpoint | Method | Description |
|----------|:------:|-------------|
| `/notifications` | GET | Get notifications |
| `/notifications/read-all` | PUT | Mark all as read |
| `/notifications/:id/read` | PUT | Mark as read |

---

## Device State History

| Endpoint | Method | Description |
|----------|:------:|-------------|
| `/devices/:id/states/history` | GET | Get device state history |
| `/devices/states` | POST | Create device state |
| `/devices/states/:id` | GET | Get device state |

---

## Versions & Features

| Endpoint | Method | Description |
|----------|:------:|-------------|
| `/versions` | GET | List all versions |
| `/versions/:id` | GET | Get version by ID |
| `/features/version/:verid` | GET | Get features by version |
| `/versions` | POST | Create version |
| `/features` | POST | Create feature |
| `/versions/:id` | PUT | Update version |
| `/features/:id` | PUT | Update feature |
| `/versions/:id` | DELETE | Delete version |
| `/features/:id` | DELETE | Delete feature |

---

## Audit Logs

| Endpoint | Method | Description |
|----------|:------:|-------------|
| `/audit` | GET | List audit logs |

---

## Code Generation

| Endpoint | Method | Description |
|----------|:------:|-------------|
| `/codegen/tools` | GET | List available tools |
| `/codegen/generate` | POST | Generate code |
| `/codegen/build` | POST | Build code |
| `/codegen/build-and-download` | POST | Build and download |
| `/codegen/download/:build_id` | GET | Download build |
| `/codegen/upload` | POST | Upload file |
| `/codegen/builds/:build_id` | DELETE | Cleanup build |

---

## Export

| Endpoint | Method | Description |
|----------|:------:|-------------|
| `/export/formats` | GET | List export formats |
| `/export/readings` | GET | Export readings |

---

## Admin

| Endpoint | Method | Description |
|----------|:------:|-------------|
| `/admin/stats` | GET | Get admin stats |