---
name: monitoring
description: Production health check, uptime monitoring, performance metrics. DevOps engineer agent için monitoring best practices. Use when this capability is needed.
metadata:
  author: erhankaraarslan
---

# Monitoring Skill

Bu skill, devops-engineer agent'ın sistemleri izlemesi ve sağlık kontrolü yapması için kullanılır.

---

## 🎯 Monitoring Prensipleri

### USE Method (Utilization, Saturation, Errors)

```
┌─────────────────────────────────────────┐
│  RESOURCE     │  U   │  S   │  E       │
├─────────────────────────────────────────┤
│  CPU          │ 75%  │ 0.5  │ 0        │
│  Memory       │ 60%  │ 0.1  │ 0        │
│  Disk         │ 40%  │ 0.0  │ 2 errors │
│  Network      │ 30%  │ 0.0  │ 1 timeout│
└─────────────────────────────────────────┘
```

### RED Method (Rate, Errors, Duration)

```
┌─────────────────────────────────────────┐
│  ENDPOINT          │  R     │  E  │  D  │
├─────────────────────────────────────────┤
│  GET /api/users    │ 150/s  │ 0%  │ 50ms│
│  POST /api/auth    │  20/s  │ 2%  │ 200ms│
│  GET /api/orders   │  80/s  │ 0.5%│ 120ms│
└─────────────────────────────────────────┘
```

---

## 📊 Kritik Metrikler

### 1. System Metrics

```bash
# CPU kullanımı
top -bn1 | grep "Cpu(s)"

# Memory kullanımı
free -h

# Disk kullanımı
df -h

# Disk I/O
iostat -x 1 5

# Network
netstat -i
```

---

### 2. Application Metrics

#### Response Time (Latency)

```typescript
// Middleware ile ölç
app.use((req, res, next) => {
  const start = Date.now()

  res.on('finish', () => {
    const duration = Date.now() - start
    metrics.recordResponseTime(req.path, duration)

    // p95 > 500ms ise alert
    if (duration > 500) {
      logger.warn('Slow response', { path: req.path, duration })
    }
  })

  next()
})
```

#### Error Rate

```typescript
let totalRequests = 0
let errorRequests = 0

app.use((req, res, next) => {
  totalRequests++

  res.on('finish', () => {
    if (res.statusCode >= 500) {
      errorRequests++
    }

    const errorRate = (errorRequests / totalRequests) * 100

    // Error rate > %1 ise critical
    if (errorRate > 1) {
      alerting.sendCritical('High error rate', { rate: errorRate })
    }
  })

  next()
})
```

#### Throughput

```typescript
// Request per second
const requestsPerMinute = []
setInterval(() => {
  const rpm = requestsPerMinute.reduce((a, b) => a + b, 0)
  const rps = rpm / 60

  metrics.record('requests_per_second', rps)
  requestsPerMinute = []
}, 60000)
```

---

### 3. Database Metrics

```sql
-- Slow queries (PostgreSQL)
SELECT
  query,
  mean_exec_time,
  calls
FROM pg_stat_statements
WHERE mean_exec_time > 1000
ORDER BY mean_exec_time DESC
LIMIT 10;

-- Connection count
SELECT count(*) FROM pg_stat_activity;

-- Database size
SELECT pg_size_pretty(pg_database_size('mydb'));
```

```bash
# MongoDB metrics
mongo --eval "db.serverStatus().connections"
mongo --eval "db.stats()"
```

---

## 🚨 Alerting Stratejisi

### Alert Seviyeleri

| Seviye | Threshold | Aksiyon |
|--------|-----------|---------|
| **INFO** | Normal olay | Log'a yaz |
| **WARNING** | Potansiyel sorun | Slack notification |
| **ERROR** | Önemli hata | Email + Slack |
| **CRITICAL** | Sistem çökmek üzere | PagerDuty + Phone call |

---

### Örnek Alert Rules

```yaml
# Prometheus alert rules
groups:
  - name: app_alerts
    rules:
      - alert: HighErrorRate
        expr: rate(http_requests_total{status=~"5.."}[5m]) > 0.01
        for: 5m
        labels:
          severity: critical
        annotations:
          summary: "High error rate detected"

      - alert: HighResponseTime
        expr: http_request_duration_seconds{quantile="0.95"} > 0.5
        for: 10m
        labels:
          severity: warning

      - alert: HighMemoryUsage
        expr: (node_memory_MemAvailable_bytes / node_memory_MemTotal_bytes) < 0.1
        for: 5m
        labels:
          severity: critical
```

---

## 🏥 Health Check Endpoints

### Liveness Check

```typescript
// /health/live - Servis ayakta mı?
app.get('/health/live', (req, res) => {
  res.status(200).json({ status: 'alive', timestamp: Date.now() })
})
```

### Readiness Check

```typescript
// /health/ready - Servis trafiğe hazır mı?
app.get('/health/ready', async (req, res) => {
  const checks = {
    database: await checkDatabase(),
    redis: await checkRedis(),
    externalAPI: await checkExternalAPI()
  }

  const allHealthy = Object.values(checks).every(check => check.healthy)

  res.status(allHealthy ? 200 : 503).json({
    status: allHealthy ? 'ready' : 'not_ready',
    checks,
    timestamp: Date.now()
  })
})

async function checkDatabase() {
  try {
    await db.query('SELECT 1')
    return { healthy: true }
  } catch (error) {
    return { healthy: false, error: error.message }
  }
}
```

### Startup Check

```typescript
// /health/startup - İlk başlatma tamamlandı mı?
let isStartupComplete = false

app.get('/health/startup', (req, res) => {
  if (isStartupComplete) {
    res.status(200).json({ status: 'started' })
  } else {
    res.status(503).json({ status: 'starting' })
  }
})

// Startup tamamlandığında
async function bootstrap() {
  await initializeDatabase()
  await warmupCache()
  await loadConfiguration()

  isStartupComplete = true
}
```

---

## 📈 Performance Monitoring

### Golden Signals

```
1. LATENCY   - Ne kadar hızlı?
2. TRAFFIC   - Ne kadar talep var?
3. ERRORS    - Ne kadar başarısız?
4. SATURATION - Kaynaklar dolu mu?
```

### Node.js Specific Metrics

```typescript
import v8 from 'v8'
import process from 'process'

function getNodeMetrics() {
  const heapStats = v8.getHeapStatistics()
  const memUsage = process.memoryUsage()

  return {
    // Heap kullanımı
    heap_total: heapStats.total_heap_size,
    heap_used: heapStats.used_heap_size,
    heap_limit: heapStats.heap_size_limit,

    // Memory
    rss: memUsage.rss, // Resident Set Size
    heap_total_mb: Math.round(memUsage.heapTotal / 1024 / 1024),
    heap_used_mb: Math.round(memUsage.heapUsed / 1024 / 1024),

    // Event Loop Lag
    event_loop_lag: getEventLoopLag(),

    // Uptime
    uptime_seconds: process.uptime(),

    // CPU
    cpu_usage: process.cpuUsage()
  }
}

// Event loop lag measurement
let lastCheck = Date.now()
function getEventLoopLag() {
  const now = Date.now()
  const lag = now - lastCheck - 1000 // Expected 1000ms
  lastCheck = now
  return lag
}

setInterval(getEventLoopLag, 1000)
```

---

## 🔍 Log Monitoring

### Structured Logging

```typescript
// ✅ Structured log (JSON)
logger.info({
  message: 'User login',
  userId: '123',
  ip: req.ip,
  userAgent: req.headers['user-agent'],
  timestamp: new Date().toISOString(),
  duration: 250
})

// Log aggregation ile kolay query:
// "Show me all logins from userId=123 in last hour"
```

### Log Levels

```typescript
const logger = createLogger({
  level: process.env.LOG_LEVEL || 'info'
})

logger.error('Critical error', { error })   // Always logged
logger.warn('Warning', { context })         // Production
logger.info('User action', { userId })      // Production
logger.debug('Variable value', { value })   // Development only
logger.trace('Function call', { args })     // Development only
```

### Log Sampling

```typescript
// High-traffic endpoint'lerde her log'u yazma
const shouldLog = Math.random() < 0.1 // %10 sample

if (shouldLog) {
  logger.info('Request processed', { path: req.path })
}
```

---

## 🛠️ Monitoring Tools & Integration

### Sentry Integration (MCP)

```typescript
// Sentry MCP ile error tracking
import { SentryMCP } from '@mcp/sentry'

app.use((err, req, res, next) => {
  // Error'ı Sentry'ye gönder
  SentryMCP.captureException(err, {
    user: { id: req.userId },
    tags: { endpoint: req.path },
    extra: { body: req.body }
  })

  res.status(500).json({ error: 'Internal server error' })
})
```

### Prometheus Metrics

```typescript
import { register, Counter, Histogram } from 'prom-client'

// Counter
const httpRequestsTotal = new Counter({
  name: 'http_requests_total',
  help: 'Total HTTP requests',
  labelNames: ['method', 'path', 'status']
})

// Histogram (latency)
const httpRequestDuration = new Histogram({
  name: 'http_request_duration_seconds',
  help: 'HTTP request duration',
  labelNames: ['method', 'path'],
  buckets: [0.1, 0.5, 1, 2, 5]
})

// Metrics endpoint
app.get('/metrics', async (req, res) => {
  res.set('Content-Type', register.contentType)
  res.end(await register.metrics())
})
```

---

## 📊 Dashboard Örneği

### Minimal Monitoring Dashboard

```typescript
// /dashboard/metrics endpoint
app.get('/dashboard/metrics', async (req, res) => {
  const metrics = {
    system: {
      uptime: process.uptime(),
      memory: process.memoryUsage(),
      cpu: process.cpuUsage()
    },
    application: {
      total_requests: totalRequests,
      error_rate: (errorRequests / totalRequests * 100).toFixed(2) + '%',
      avg_response_time: calculateAvgResponseTime() + 'ms'
    },
    database: {
      active_connections: await getDbConnections(),
      slow_queries: await getSlowQueries()
    },
    alerts: {
      active: await getActiveAlerts(),
      recent: await getRecentAlerts(24) // Last 24h
    }
  }

  res.json(metrics)
})
```

---

## 🚀 Production Monitoring Checklist

Session başında kontrol et:

- [ ] Health check endpoint'leri çalışıyor mu?
- [ ] Log aggregation sistemi aktif mi?
- [ ] Error tracking (Sentry) kurulu mu?
- [ ] Alerting rules tanımlı mı?
- [ ] Metrics endpoint expose edilmiş mi?
- [ ] Dashboard erişilebilir mi?
- [ ] Backup monitoring çalışıyor mu?
- [ ] SSL certificate expiry izleniyor mu?

---

## 🔔 Alert Response Playbook

### Critical Alert Geldiğinde

```bash
# 1. Durumu doğrula
curl https://myapp.com/health/ready

# 2. Son log'ları kontrol et
tail -f -n 200 /var/log/app/error.log

# 3. Resource kullanımı
top -bn1
free -h
df -h

# 4. Service durumu
systemctl status myapp
docker ps

# 5. Son değişiklikleri gör
git log --oneline -10

# 6. Gerekirse rollback
git revert HEAD
./deploy.sh

# 7. Incident report yaz
# docs/incidents/YYYY-MM-DD-incident.md
```

---

## 📝 Monitoring Best Practices

### DO ✅

- **Baseline oluştur** - Normal değerleri bil
- **Trend analizi** - Zaman içinde nasıl değişiyor?
- **Alert fatigue önle** - Çok alert kötü alert
- **SLA tanımla** - %99.9 uptime hedefle
- **Regular review** - Dashboard'u haftada 1 gözden geçir
- **Documentation** - Alert playbook yaz

### DON'T ❌

- **Reactive monitoring** - Sadece hata olunca bakma
- **Metric overload** - 100 metric > 10 critical metric
- **Silent failures** - Error'ları yutma
- **Production debugging** - Monitor et, debug etme
- **Ignore warnings** - Warning bugün, critical yarın

---

## 🎯 SLI/SLO/SLA

### Service Level Indicators (SLI)

```
Availability = (Successful Requests / Total Requests) × 100
Latency p95 = 95th percentile response time
Error Rate = (Failed Requests / Total Requests) × 100
```

### Service Level Objectives (SLO)

```
Target Availability: 99.9% (43.2 min downtime/month)
Target p95 Latency: < 200ms
Target Error Rate: < 0.1%
```

### Service Level Agreements (SLA)

```
Guaranteed Availability: 99.5%
If < 99.5%: 10% service credit
If < 99.0%: 25% service credit
```

---

## 🔗 Monitoring Stack Örnekleri

### Stack 1: Open Source

```
Prometheus → Metric collection
Grafana → Visualization
AlertManager → Alerting
Loki → Log aggregation
Jaeger → Distributed tracing
```

### Stack 2: Cloud Native

```
CloudWatch (AWS) → Metrics + Logs
Datadog → APM + Monitoring
Sentry → Error tracking
PagerDuty → On-call alerting
```

### Stack 3: Minimal (MCP)

```
Sentry MCP → Error tracking
Custom /metrics endpoint → Prometheus scrape
GitHub Actions → Uptime monitoring
Slack → Alerting
```

---

## 📚 Kaynaklar

- [SRE Book - Google](https://sre.google/sre-book/table-of-contents/)
- [Prometheus Best Practices](https://prometheus.io/docs/practices/naming/)
- [The Four Golden Signals](https://sre.google/sre-book/monitoring-distributed-systems/)

---

**Son Güncelleme:** 2026-01-26
**Kullanıcı:** devops-engineer agent
**İlgili Skill:** error-recovery, debugging

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/erhankaraarslan) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
