---
name: api-cache-analyzer
description: Analyzes Redis cache performance including hit rates, key distribution, memory usage, TTL patterns, and tenant isolation compliance
metadata:
  author: rimthan-lab
---

## Purpose

Analyzes Redis cache performance including hit rates, key distribution, memory usage, TTL patterns, and invalidation effectiveness.

## Responsibilities

1. **Performance Analysis**
   - Calculate cache hit/miss rates
   - Identify frequently accessed keys
   - Measure cache response times
   - Track cache effectiveness

2. **Memory Analysis**
   - Check total memory usage
   - Identify large keys
   - Check key expiration patterns
   - Find keys without TTL

3. **Tenant Analysis**
   - Verify tenant prefix compliance
   - Check for cross-tenant key collisions
   - Analyze per-tenant cache usage
   - Identify tenants with high cache usage

4. **Invalidation Analysis**
   - Track cache invalidation patterns
   - Identify stale data
   - Check invalidation effectiveness
   - Find invalidation bugs

## Metrics Tracked

### Performance Metrics

- `cache.hit_rate` - Cache hit percentage
- `cache.miss_rate` - Cache miss percentage
- `cache.avg_response_time` - Average cache operation time
- `cache.ops_per_second` - Operations per second

### Memory Metrics

- `cache.memory_used` - Total memory used
- `cache.memory_total` - Total memory available
- `cache.key_count` - Total number of keys
- `cache.avg_key_size` - Average key size

### Tenant Metrics

- `cache.tenant_key_count` - Keys per tenant
- `cache.tenant_memory` - Memory per tenant
- `cache.tenant_hit_rate` - Hit rate per tenant

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/rimthan-lab) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
