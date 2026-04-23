---
name: swarm
description: N coordinated agents on shared task list (compatibility facade over team) Use when this capability is needed.
metadata:
  author: yeachan-heo
---

# Swarm (Compatibility Facade)

Swarm is a compatibility alias for the `/team` skill. All swarm invocations are routed to the Team skill's staged pipeline.

## Usage

```
/swarm N:agent-type "task description"
/swarm "task description"
```

## Behavior

This skill is identical to `/team`. Invoke the Team skill with the same arguments:

```
/team <arguments>
```

Follow the Team skill's full documentation for staged pipeline, agent routing, and coordination semantics.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/yeachan-heo) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
