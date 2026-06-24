---
name: redis-cli
description: Inspect ephemeral Redis coordination state used by Hekate v2 Use when this capability is needed.
metadata:
  author: spideynolove
---

# Redis CLI Operations

Redis only stores transient coordination state in Hekate v2. Durable epic and task state lives in SQLite.

## Coordination Keys

```bash
redis-cli keys "task:*:claim"
redis-cli keys "agent:*:heartbeat"
redis-cli keys "session:*:task_id"
redis-cli keys "quota:*"
redis-cli keys "verify:prefetch:*"
```

## Common Checks

```bash
redis-cli GET "task:{task_id}:claim"
redis-cli GET "agent:{session_id}:heartbeat"
redis-cli GET "session:{session_id}:task_id"
redis-cli GET "session:{session_id}:provider"
redis-cli GET "quota:claude:count"
redis-cli GET "quota:claude:limit"
```

## Safe Resets

```bash
redis-cli --scan --pattern "task:*:claim" | xargs -r redis-cli del
redis-cli --scan --pattern "agent:*:heartbeat" | xargs -r redis-cli del
redis-cli SET "quota:claude:count" "0"
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/spideynolove) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
