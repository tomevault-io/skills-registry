---
name: api-tracing-otel
description: Generate OpenTelemetry instrumented handlers and services with span creation, attributes (tenantId, actorId, operation), and error recording. Use when adding observability or distributed tracing. Use when this capability is needed.
metadata:
  author: rimthan-lab
---

# OpenTelemetry Tracing

## Purpose

Generate command/query handlers and services with OpenTelemetry instrumentation for distributed tracing, including span creation, attributes, and error recording.

## When to Use

- Adding observability to CQRS handlers
- Creating instrumented services
- Implementing distributed tracing
- Performance monitoring

## What It Generates

### Instrumented Handler Template

```
apps/api/src/modules/{feature}/commands/{operation}/{operation}.handler.ts
apps/api/src/modules/{feature}/services/{feature}.service.ts
```

## Patterns Enforced

### Span Creation

All handlers create spans:

- Span name: `{feature}.{operation}`
- Attributes: tenantId, actorId, operation, resource
- Error recording on exceptions

### Span Attributes

Standard attributes:

- `tenant.id` - Tenant ID
- `actor.id` - User performing action
- `operation.name` - Operation name
- `operation.type` - command/query/event
- `resource.id` - Resource being operated on
- `error.type` - Error type (on failure)

### Span Status

- OK on success
- Error on failure (with error recording)

## Usage Example

```bash
/skill tracing-otel --name=CreateUser --type=command
```

## Related Files

- [Feature CQRS](../../core/feature-cqrs/SKILL.md) - CQRS handlers with tracing
- [Cache Redis](../cache-redis/SKILL.md) - Cache operations with tracing

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/rimthan-lab) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
