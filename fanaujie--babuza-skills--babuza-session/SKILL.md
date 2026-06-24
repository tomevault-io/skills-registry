---
name: babuza-session
description: | Use when this capability is needed.
metadata:
  author: fanaujie
---

# Babuza Session Skill

> **Package:** `github.com/fanaujie/babuza/pkg/session`
>
> Client session management for exactly-once semantics and idempotent command execution.

You are an expert at `babuza` session management. Help users by:
- **Writing code**: Configure LRU, Expire, or NoOp session managers.
- **Answering questions**: Explain exactly-once semantics, session lifecycle, and eviction strategies.

## Documentation

Refer to the local files for detailed API:
- `./references/session-api.md` - Session interfaces, options, and eviction strategies.

## Key Patterns

### 1. Configuring Session Manager via Builder

Select the session management strategy using `BabuzaComponentBuilder`.

```go
import "github.com/fanaujie/babuza/pkg/builder"

func buildWithSession() *ibabuza.BabuzaComponent {
    return builder.NewBabuzaComponentBuilder(&builder.BabuzaComponentConfig{
        // Select Session Type: LRUSession, ExpireSession, or NoOpSession
        SessionType: builder.LRUSession,
    }).Build()
}
```

### 2. Configuring LRU Session Options

Limit the maximum number of concurrent sessions.

```go
import (
    "github.com/fanaujie/babuza/pkg/builder"
    "github.com/fanaujie/babuza/pkg/session"
)

func buildLruSession() {
    builder.NewBabuzaComponentBuilder(&builder.BabuzaComponentConfig{
        SessionType: builder.LRUSession,
    }).SetSessionOptions(
        session.SetLruMgrOptionsWithMaxSessions(256),
    ).Build()
}
```

### 3. Configuring Expire Session Options

Set the session expiration timeout.

```go
import (
    "time"
    "github.com/fanaujie/babuza/pkg/builder"
    "github.com/fanaujie/babuza/pkg/session"
)

func buildExpireSession() {
    builder.NewBabuzaComponentBuilder(&builder.BabuzaComponentConfig{
        SessionType: builder.ExpireSession,
    }).SetSessionOptions(
        session.SetExpiredMgrOptionsWithExpiredTime(time.Hour),
    ).Build()
}
```

## Session Types

| Type | Builder Constant | Eviction Strategy | Default |
|------|------------------|-------------------|---------|
| **LRU** | `builder.LRUSession` | Evicts least-recently-used when max reached | maxSessions=128 |
| **Expire** | `builder.ExpireSession` | Auto-expires after timeout | expiredTime=30min |
| **NoOp** | `builder.NoOpSession` | No session tracking | - |

## When to Use Each Type

| Scenario | Recommended Type |
|----------|------------------|
| Fixed number of clients | LRU |
| Dynamic/short-lived clients | Expire |
| No idempotency needed | NoOp |
| High-throughput, retry-safe | LRU or Expire |

## When Writing Code

1. **Exactly-Once**: Use sessions with `SequenceNumber` to prevent duplicate command execution on retries.
2. **Session Registration**: Always `RegisterSession` before proposing commands.
3. **Cleanup**: Call `UnregisterSession` when a client disconnects to free resources.

## When Answering Questions

1. **Idempotency**: Explain that sessions cache the result of the highest applied `SequenceNumber`, returning cached results for duplicate requests.
2. **LRU vs Expire**: LRU is better for fixed client pools; Expire is better for dynamic clients that may disconnect without cleanup.
3. **NoOp**: Warn that NoOp provides no duplicate protection and should only be used for read-only or truly idempotent operations.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/fanaujie) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
