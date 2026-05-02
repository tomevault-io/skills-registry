---
name: instrumentation-planning
description: Plan backend observability using RED + USE + 4 Golden Signals + JTBD Use when this capability is needed.
metadata:
  author: nexus-labs-automation
---

# Instrumentation Planning

> "What job is this telemetry helping someone accomplish?"

Every metric/span should answer a job. If you can't name the job, don't add the telemetry.

## Framework Selection

| Service Type | Use |
|--------------|-----|
| HTTP/gRPC APIs | RED (Rate, Errors, Duration) |
| Resources (pools, memory) | USE (Utilization, Saturation, Errors) |
| Comprehensive SRE | 4 Golden Signals |

## Prioritization Tiers

| Tier | What | Examples |
|------|------|----------|
| **T0: Foundation** | Must have | Service tags, HTTP middleware, error tracking, health checks |
| **T1: Performance** | Should have | RED per endpoint, DB tracing, external service spans, context propagation |
| **T2: Resources** | Should have | USE for pools, memory/GC, queue depth |
| **T3: Business** | Nice to have | JTBD tags, user tier, feature flags |
| **T4: Resilience** | Nice to have | Circuit breaker state, retry counts, timeouts |

## JTBD Context

Link observability to user value:

| Job | Success | Failure | Friction |
|-----|---------|---------|----------|
| "Place order" | Order created <2s | Order error | Retries >0, duration >5s |
| "Process payment" | Payment success | Declined | Gateway timeout |

## Anti-Patterns

- **Measure everything** → Noise, cost, no signal
- **High-cardinality labels** → User IDs explode storage
- **100% sampling** → Unnecessary overhead
- **Missing context** → Can't debug without job/step

## Output

Present prioritized instrumentation plan organized by tier, with specific metrics and implementation order.

## References

Load based on need:
- `references/methodology/red-methodology.md`
- `references/methodology/use-methodology.md`
- `references/methodology/four-golden-signals.md`
- `references/methodology/jtbd-for-backend.md`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/nexus-labs-automation) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
