# Prometheus Configuration Guide for `goredis`

This guide explains how to set up, configure, and operate Prometheus metrics monitoring for the `goredis` library in both development/testing and production environments.

---

## 1. High Cardinality Warning (Critical)

> [!WARNING]
> By default, Prometheus metric labels should not contain high-cardinality values such as user IDs, UUIDs, or individual cache keys. Doing so will cause Prometheus memory consumption to explode.
> 
> Always use a **`KeyMapper`** in production to group metrics by key-space prefixes rather than individual keys:
> 
```go
promMetrics, err := prometheus.New(prometheus.Options{
    Namespace: "production",
    Subsystem: "cache",
    KeyMapper: func(key string) string {
        // Group high-cardinality keys like user:12345 or user:99887 into "user"
        if strings.HasPrefix(key, "user:") {
            return "user"
        }
        return "other"
    },
})
```

---

## 2. Local Testing and Development

### Running with Docker Compose
To run Prometheus locally alongside your Go application, use a Docker Compose setup:

#### `prometheus.yml` (Scrape Configuration)
```yaml
global:
  scrape_interval: 5s # Fast scrape interval for testing feedback

scrape_configs:
  - job_name: 'goredis-app'
    static_configs:
      # host.docker.internal resolves to the host machine from inside the container
      - targets: ['host.docker.internal:8080'] 
```

#### `docker-compose.yml`
```yaml
version: '3.8'
services:
  prometheus:
    image: prom/prometheus:latest
    container_name: prometheus
    volumes:
      - ./prometheus.yml:/etc/prometheus/prometheus.yml
    ports:
      - "9090:9090"
    extra_hosts:
      - "host.docker.internal:host-gateway"
```

Start the container:
```bash
docker-compose up -d
```
Access the Prometheus dashboard at **http://localhost:9090**.

### Writing Unit/Integration Tests
To verify metrics in tests without starting HTTP servers or using global state, use a private `prometheus.Registry` and collect metrics programmatically:

```go
func TestCacheMetrics(t *testing.T) {
    // Create a local registry
    reg := prometheus.NewRegistry()
    
    // Register goredis metrics to the local registry
    m, _ := goredisprom.New(goredisprom.Options{
        Registry: reg,
    })
    
    // Initialize cache with local metrics
    cache := goredis.New(redisClient, goredis.WithMetrics(m))
    
    // Perform operations
    cache.Get(...)
    
    // Gather and verify metrics
    metricFamilies, _ := reg.Gather()
    // Iterate through families to verify myapp_cache_goredis_cache_hits_total is updated
}
```

---

## 3. Production Configuration

In production, you want automatic target discovery, security, and alerting rules.

### A. Exposing `/metrics` Safely
Expose the metrics endpoint on a private port/admin interface rather than on the public-facing HTTP server to avoid exposing internal stats to the internet.

```go
// Run application on port 8000, but metrics on a private internal port 8080
go func() {
    privateMux := http.NewServeMux()
    privateMux.Handle("/metrics", promhttp.Handler())
    http.ListenAndServe(":8080", privateMux) // Expose internally only
}()
```

### B. Kubernetes Deployment (Prometheus Operator)
If you deploy on Kubernetes, use a `ServiceMonitor` for automatic target discovery:

```yaml
apiVersion: monitoring.coreos.com/v1
kind: ServiceMonitor
metadata:
  name: goredis-app-monitor
  labels:
    release: prometheus-stack
spec:
  selector:
    matchLabels:
      app: my-go-application
  endpoints:
  - port: metrics
    path: /metrics
    interval: 15s
```

### C. Standard Prometheus Configuration
If using standard static configs or DNS discovery in production:

```yaml
scrape_configs:
  - job_name: 'production-goredis'
    scrape_interval: 15s
    metrics_path: /metrics
    kubernetes_sd_configs:
      - role: pod
    relabel_configs:
      - source_labels: [__meta_kubernetes_pod_annotation_prometheus_io_scrape]
        action: keep
        regex: true
```

---

## 4. Recommended Alerts

Add these alert rules to your Prometheus setup to detect cache degradation:

### 1. Low Cache Hit Rate
Triggers if the cache hit rate drops below 50%, indicating inefficient caching strategies or outdated caches.
```yaml
alert: LowCacheHitRate
expr: |
  sum(rate(myapp_cache_goredis_cache_hits_total[5m]))
  /
  (sum(rate(myapp_cache_goredis_cache_hits_total[5m])) + sum(rate(myapp_cache_goredis_cache_misses_total[5m]))) < 0.5
for: 10m
labels:
  severity: warning
annotations:
  summary: "Cache hit rate is low ({{ $value | printf \"%.2f\" }})"
```

### 2. High Source Fetch Latency
Triggers if fetching from the origin database/API takes longer than 1 second (p95 latency).
```yaml
alert: HighCacheFetchLatency
expr: histogram_quantile(0.95, sum(rate(myapp_cache_goredis_cache_fetch_duration_seconds_bucket[5m])) by (le)) > 1.0
for: 5m
labels:
  severity: critical
annotations:
  summary: "95th percentile database fetch latency is high: {{ $value }}s"
```
