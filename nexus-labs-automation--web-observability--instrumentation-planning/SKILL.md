---
name: instrumentation-planning
description: Plan what to measure in web applications. Use when starting observability or prioritizing instrumentation. Use when this capability is needed.
metadata:
  author: nexus-labs-automation
---

# Instrumentation Planning

Strategic guidance for what to measure in web applications.

## Core Question

For each user job, ask:
- **Did it complete?** → completion rate
- **How long?** → duration (p50, p95, p99)
- **What failed?** → error type, context
- **Did they give up?** → drop-off rate
- **How smooth?** → Core Web Vitals, friction signals

## Priority Tiers

| Tier | Focus | Priority |
|------|-------|----------|
| 1 | Errors + source maps | P0 - Day 1 |
| 2 | Core Web Vitals | P0 - Day 1 |
| 3 | User context + breadcrumbs | P0 - Week 1 |
| 4 | Route transitions + API tracing | P1 - Week 2 |
| 5 | Business metrics + synthetic tests | P2 - Month 1 |

## Core Web Vitals Thresholds

| Metric | Good | Needs Improvement | Poor |
|--------|------|-------------------|------|
| LCP | ≤2.5s | ≤4.0s | >4.0s |
| INP | ≤200ms | ≤500ms | >500ms |
| CLS | ≤0.1 | ≤0.25 | >0.25 |

## OTel-Compatible Naming

Use now for easier migration later:
- `http.request.method` not `method`
- `http.request.duration` not `apiCallTime`
- `url.path` not `page`

## Anti-Patterns

See `references/anti-patterns.md` for:
- Measuring everything (noise, bundle bloat)
- Skipping source maps
- Blocking main thread
- PII in breadcrumbs

## Implementation Details

See `references/instrumentation-patterns.md` for:
- Detailed 5-tier checklist
- Span naming conventions
- Sampling strategies

See `references/jtbd.md` for Jobs-to-be-Done framework.

## Related Skills

- `skills/core-web-vitals` - Tier 2 implementation
- `skills/error-tracking` - Tier 1 implementation
- `skills/source-map-setup` - Tier 1 implementation

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/nexus-labs-automation) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
