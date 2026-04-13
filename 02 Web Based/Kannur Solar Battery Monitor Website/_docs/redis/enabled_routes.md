# Redis-enabled API routes

The following API routes are backed by Redis cache through the decorated repositories in `main.go`.

## Reading-related routes
- `GET /api/devices/:id/readings`
- `GET /api/devices/:id/readings/range`
- `GET /api/devices/:id/readings/progressive`
- `GET /api/devices/:id/readings/interval`
- `GET /api/devices/:id/connected/:cid/readings`
- `GET /api/readings`
- `GET /api/locations/:id/readings/seven`

## Device-related routes
- `GET /api/devices`
- `GET /api/devices/recent`
- `GET /api/devices/:id`
- `GET /api/devices/:id/connected`
- `GET /api/devices/:id/connected/:cid/readings`
- `GET /api/devices/my`
- `GET /api/devices/my/stats`
- `GET /api/devices/search`
- `GET /api/devices/search/microcontrollers`
- `GET /api/devices/search/sensors`
- `GET /api/devices/microcontrollers`
- `GET /api/devices/microcontrollers/stats`
- `GET /api/devices/types`
- `GET /api/devices/:id/type`
- `GET /api/devices/types/hardware`
- `GET /api/devices/types/sensors`

## User-related routes
- `GET /api/users`
- `GET /api/users/:id`
- `GET /api/users/:id/profile`
- `GET /api/profile`

## Location-related routes
- `GET /api/locations`
- `GET /api/locations/:id`
- `GET /api/locations/search`
- `GET /api/locations/:id/devices`

## Device type routes
- `GET /api/device-types`

## Notes
- Cache is applied at repository layer for `deviceRepo`, `readingRepo`, `locationRepo`, `userRepo`, and `deviceTypesRepo`.
- Read-heavy endpoints listed above are the ones that benefit directly from Redis cache.
