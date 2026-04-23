---
name: redis-cache-skill
description: Use this skill to implement Redis cache patterns. It provides templates for Redis initialization (standalone/cluster) and advanced caching strategies (MultiGet, Pipeline, SingleFlight).
metadata:
  author: penitence1992
---

# Redis Cache Implementation

This skill provides standard patterns for integrating Redis into go-zero services.

## Execution Steps

### Step 1: Initialize Redis Client
Create `infra/cfg/redis.go` (or similar utility path) using the standard template to handle connection logic.

**Template Usage:**
- Source: `templates/redis.go.tmpl`
- Target: `infra/cfg/redis.go`
- **Variables:**
  - `{{PACKAGE_NAME}}`: Package name (e.g., `cfg`)

### Step 2: Add Configuration
Update Service config struct (`internal/config/config.go`) and YAML file (`etc/service.yaml`).

**Config Struct:**
```go
type Config struct {
    // ...
    RedisConf cfg.RedisConf
}
```

**YAML Config:**
```yaml
RedisConf:
  Host: 127.0.0.1:6379
  Type: node # or cluster
  Pass: ""
```

### Step 3: Initialize in ServiceContext
In `internal/svc/service_context.go`:

```go
// Initialize Redis
rds, err := cfg.InitRedis(c.RedisConf)
if err != nil {
    panic(err)
}
s.Redis = rds
```

## Advanced Patterns

### MultiGet with Pipeline
Refer to `RdsMultiGet` in the references or previous code examples for batch fetching to reduce network RTT.

### SingleFlight
Use `go-zero/core/stores/cache` for anti-breakdown patterns.

## Templates Location
- `templates/redis.go.tmpl`: Universal Redis client initializer.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/penitence1992) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
