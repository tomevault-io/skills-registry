---
name: rpc
description: Guidelines for RPC interface design, resilience, and tracing. Use when this capability is needed.
metadata:
  author: andrewhowdencom
---

# RPC

## Design
- **Context**: Every RPC entry point or client call MUST accept `context.Context` as the first argument.
- **Tracing**: All RPCs MUST be instrumented with OpenTelemetry.
  - Server: Extract context, start span.
  - Client: Inject context, start span.

## Resilience

### Timeouts
Set a deadline on **every** outgoing request.
```go
ctx, cancel := context.WithTimeout(context.Background(), 2*time.Second)
defer cancel()
```

| Scenario | Timeout |
| :--- | :--- |
| Inter-service (same datacenter) | 2s |
| External API | 5-10s |
| User-facing | 1s |
| Background jobs | 30-60s |

### Retries
Use exponential backoff with jitter for transient failures.
- **gRPC**: Use `grpc_retry` middleware.
- **HTTP**: Use `go-retryablehttp`.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/andrewhowdencom) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
