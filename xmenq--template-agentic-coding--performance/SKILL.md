---
name: performance
description: Guidelines for optimization, profiling, caching strategies, and performance budgets Use when this capability is needed.
metadata:
  author: xmenq
---

# Performance Skill

## When to use this skill

Use when optimizing existing code, implementing caching, setting up profiling, or when performance budgets are at risk.

---

## Performance Principles

### 1. Measure first, optimize second
- Never optimize without a benchmark showing the problem
- Profile to find the actual bottleneck — intuition is often wrong
- Set concrete, measurable budgets and track them

### 2. The fastest code is code that doesn't run
- Cache expensive results
- Avoid unnecessary computation
- Lazy-load what isn't immediately needed
- Short-circuit early when possible

### 3. Optimize for the common case
- 80% of time is spent in 20% of code — find that 20%
- Don't optimize cold paths at the expense of hot paths

---

## Performance Budgets

Define and enforce these in CI:

| Metric | Target | Measured by |
|--------|--------|-------------|
| App startup | < ___ms | Startup trace/log |
| API response (p50) | < ___ms | Request metrics |
| API response (p95) | < ___ms | Request metrics |
| API response (p99) | < ___ms | Request metrics |
| Page load (LCP) | < ___s | Lighthouse / Web Vitals |
| Time to interactive | < ___s | Lighthouse |
| JS bundle size | < ___KB | Build output |
| Memory usage (steady state) | < ___MB | Runtime metrics |
| Database query (p95) | < ___ms | Query metrics |

> Fill in your actual targets. Leave blank = no budget = no accountability.

---

## Profiling Checklist

When investigating a performance issue:

1. **Reproduce consistently** — create a repeatable test case
2. **Measure baseline** — record current performance numbers
3. **Profile** — use the appropriate tool for your stack:
   - **CPU**: profiler (Chrome DevTools / `py-spy` / `pprof`)
   - **Memory**: heap snapshot (Chrome / `tracemalloc` / `pprof`)
   - **Network**: request waterfall (DevTools Network tab)
   - **Database**: slow query log + EXPLAIN plans
4. **Identify the bottleneck** — find the single biggest contributor
5. **Fix one thing** — make one change, re-measure
6. **Verify improvement** — compare against baseline
7. **Document** — record what you changed and the before/after numbers

---

## Caching Strategy

### Cache layers
```
Client Cache → CDN → App Cache → Database Cache → Database
(fastest)                                        (slowest)
```

### When to cache
| Scenario | Cache strategy |
|----------|---------------|
| Static assets (images, CSS, JS) | CDN + long-lived browser cache |
| API responses that rarely change | HTTP cache headers (ETag, Cache-Control) |
| Expensive computations | In-memory cache with TTL |
| Database query results | Redis/Memcached with invalidation |
| Session data | Server-side session store |

### Cache invalidation rules
- **Set TTL (time-to-live)** — every cached item must expire
- **Invalidate on write** — when data changes, invalidate related caches
- **Cache keys must be deterministic** — same input = same cache key
- **Monitor hit rate** — < 80% hit rate suggests cache configuration issues

### Cache anti-patterns
- ❌ Caching without TTL (stale data forever)
- ❌ Caching user-specific data in shared cache
- ❌ Cache stampede (many requests rebuilding cache simultaneously)
- ❌ Caching errors (negative caching without short TTL)

---

## Common Optimizations

### Backend
| Technique | When to use |
|-----------|------------|
| Database indexing | Slow queries on specific columns |
| Connection pooling | High-throughput database access |
| Batch operations | Processing many items (use bulk insert/update) |
| Async processing | Non-blocking operations (email, notifications) |
| Pagination | Large result sets |
| Compression | Large response bodies (gzip/brotli) |

### Frontend
| Technique | When to use |
|-----------|------------|
| Code splitting | Large JS bundles (lazy-load routes) |
| Image optimization | Heavy pages (WebP/AVIF, srcset, lazy-load) |
| Virtual scrolling | Long lists (>100 items) |
| Debounce/throttle | Frequent events (scroll, resize, search input) |
| Service workers | Offline support, background sync |
| Critical CSS | Slow first paint (inline above-fold CSS) |

---

## Load Testing

Before launching or major changes:

1. **Define load expectations** — expected concurrent users, requests/sec
2. **Create realistic scenarios** — simulate real user behavior, not just hits
3. **Test at 2x expected load** — find the breaking point
4. **Monitor during test** — CPU, memory, response times, error rates
5. **Document results** — include in the exec plan validation evidence

---

## PR Checklist for Performance Changes

- [ ] Baseline measured before changes
- [ ] Improvement measured after changes (with numbers)
- [ ] No regression in other areas
- [ ] Performance budgets still met
- [ ] Cache invalidation tested
- [ ] Load tested if applicable
- [ ] Before/after numbers documented in PR

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/xmenq) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
