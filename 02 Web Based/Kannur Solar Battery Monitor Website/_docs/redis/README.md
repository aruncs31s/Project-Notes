# Redis Caching Documentation

The SKVMS backend uses Redis to cache expensive "bulk reading" database queries. This significantly reduces database load and improves response times for dashboard and historical data requests.

## Architecture

The caching is implemented using a **Decorator Pattern**. Instead of modifying the database repository logic, we wrap the `ReadingRepository` with a `cachedReadingRepository`.

- **Source Code**: [internal/repository/reading_cache.go](file:///home/aruncs/Projects/Smart-City/skvms/internal/repository/reading_cache.go)
- **Cache Interface**: [internal/cache/cache.go](file:///home/aruncs/Projects/Smart-City/skvms/internal/cache/cache.go)
- **Redis Connection**: [internal/cache/redis.go](file:///home/aruncs/Projects/Smart-City/skvms/internal/cache/redis.go)

## Configuration

Caching is configured via the `.env` file:

```bash
# Example REDIS_URL
REDIS_URL=redis://localhost:6379
```

If `REDIS_URL` is missing or the Redis server is unreachable, the system automatically falls back to a `NoopCache`. This ensures the application remains functional even without a Redis instance (Graceful Degradation).

## Cache Strategy

### Time-to-Live (TTL)

Different types of data have different expiration times to balance freshness and performance:

| Method | Data Type | TTL |
| :--- | :--- | :--- |
| `ListByDevice` | Today's Readings | 60 seconds |
| `ListByDeviceAndDateRange` | Historical Range | 5 minutes |
| `ListByDeviceWithInterval` | Sampled Readings | 2 minutes |
| `ListByDeviceProgressive` | Dashboard (Last 100) | 30 seconds |
| `GetStats` | Aggregated Stats | 2 minutes |
| `SevenDaysReadingsByLocation` | Weekly Overview | 10 minutes |

### Invalidation

The cache follows a **Write-Through Invalidation** strategy. When a new reading is recorded for a device via `RecordEssentialReadings`, all cached keys associated with that device are wiped using a pattern match: `readings:device:{id}:*`.

## Debugging and Verification

### Logs

The system logs cache activity at the `DEBUG` level:

- `[cache] HIT ...`: Data was served from Redis.
- `[cache] unmarshal error`: Potential data corruption or schema change (rare).
- `[cache] Redis connected`: Successful connection on startup.

### CLI Inspection

You can use `redis-cli` to inspect cached data:

```bash
# List all reading cache keys
redis-cli KEYS "readings:*"

# Check TTL of a specific key
redis-cli TTL "readings:device:1:today"

# Manually clear all caches
redis-cli FLUSHALL
```

## Performance Benefits

By serving historical data from memory (Redis) instead of disk (MySQL), response times for bulk data endpoints typically drop from **>100ms** to **<5ms**.
