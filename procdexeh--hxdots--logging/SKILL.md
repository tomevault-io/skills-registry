---
name: logging
description: Implement wide events and canonical log lines for high-cardinality, high-dimensionality observability. Use when adding logging, improving observability, debugging production issues, or reviewing logging code. Use when this capability is needed.
metadata:
  author: procdexeh
---

# Logging

One wide event per request per service. Not 15 scattered log lines.

## Should I Use Wide Events?

```
START HERE:
├─ New service/endpoint?
│   → YES. Add wide event middleware. See templates/middleware.ts
├─ Adding a log statement to a handler?
│   → STOP. Enrich the wide event instead.
├─ Debugging production issue?
│   → Check: do events have user_id, request_id, business context?
│   → Missing fields? Add them. See references/wide-events.md
├─ Reviewing existing logging?
│   → Scattered console.log/logger calls? Consolidate to wide events.
│   → Incremental: start with critical paths, don't rewrite everything at once.
└─ Concerned about log volume/cost?
    → See references/implementation.md § Tail Sampling
```

## Quick Reference

| Code You See | What To Do |
|-------------|------------|
| `console.log("Payment failed")` | Enrich `event.error` with structured details |
| `logger.info("User logged in")` | Add user context to wide event |
| 5+ log statements in one handler | Consolidate into single wide event |
| `log.debug()` scattered everywhere | Remove. Enrich the wide event instead |
| `catch (err) { logger.error(err) }` | Set `event.error`, let middleware emit |
| New API endpoint with no logging | Add middleware, enrich in handler |

## Non-Negotiable Fields

Every wide event MUST have:

| Field | Why |
|-------|-----|
| `request_id` | Correlation across services |
| `timestamp` | Ordering, time-range queries |
| `duration_ms` | Performance analysis |
| `service`, `version` | Where it ran, which deploy |
| `method`, `path`, `status_code` | What happened |
| `user.id` | Who (even if anonymous session) |
| `outcome` | `"success"` or `"error"` |

## The Pattern (Any Language)

```
1. Middleware creates mutable event object at request start
2. Attach event to request context (ctx, req, locals, etc.)
3. Handlers enrich event with business context as they process
4. finally{} block: set duration_ms, emit event via structured logger
5. Tail sampling decides what to store
```

For copy-paste middleware: See `templates/middleware.ts`
For full implementation details: See [implementation.md](./references/implementation.md)

## What Goes In a Wide Event

| Category | Example Fields | When to Add |
|----------|---------------|-------------|
| **Request** | method, path, status, duration_ms, request_id, trace_id | Always (middleware) |
| **Service** | name, version, region, deployment_id | Always (middleware) |
| **User** | id, subscription, account_age_days, lifetime_value | Auth middleware or handler |
| **Business** | cart_total, item_count, feature_flags, coupon_code | Handler, domain-specific |
| **Error** | type, code, message, retriable, upstream_service | catch blocks |
| **Dependencies** | db_query_count, cache_hit, external_call_latency_ms | After each dep call |

Full field taxonomy and examples: See [wide-events.md](./references/wide-events.md)

## Anti-Patterns

| Don't | Why | Do Instead |
|-------|-----|------------|
| String interpolation in logs | Unsearchable, unqueryable | Structured key-value fields |
| Log only on errors | Misses success context for comparison | Always emit (success AND failure) |
| Scattered debug statements | Noise, no correlation | Single enriched event |
| Log sensitive data (passwords, tokens, PII) | Security/compliance risk | Redact or omit |
| Convert all logging at once | Risk of breaking existing monitoring | Incremental: critical paths first |
| 50+ fields on hot paths synchronously | Performance impact | Async emission, buffer |

## Key Concepts

**Cardinality**: Unique values per field. High-cardinality (user_id, request_id) = useful for debugging. Low-cardinality (http_method) = useful for aggregation.

**Dimensionality**: Number of fields per event. More dimensions = more questions answerable without redeployment.

**Tail Sampling**: Decide AFTER request completes what to keep. Always keep: errors, slow requests (>p99), VIP users, feature flag rollouts. Random sample the rest (1-10%).

**Wide Event ≠ Structured Logging**: Structured logging is JSON format (table stakes). Wide events are a philosophy: one comprehensive event per request with all context.

**OTel is a delivery mechanism**: OpenTelemetry standardizes collection/export. It doesn't decide what to capture. You still must instrument with business context.

**Wide events complement traces**: Tracing = request flow across services. Wide events = context within a service. Best case: wide events ARE enriched trace spans.

## In This Reference

| File | Purpose |
|------|---------|
| [wide-events.md](./references/wide-events.md) | Full field taxonomy, example events, field naming |
| [implementation.md](./references/implementation.md) | Middleware patterns, enrichment, tail sampling |
| `templates/middleware.ts` | Copy-paste TypeScript middleware |

## Reading Order

| Task | Files |
|------|-------|
| New to wide events | This file only |
| Implementing in codebase | This + templates/middleware.ts |
| Adding fields / reviewing logs | This + wide-events.md |
| Full implementation with sampling | This + implementation.md |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/procdexeh) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
