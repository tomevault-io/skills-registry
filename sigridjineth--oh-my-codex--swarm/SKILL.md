---
name: swarm
description: N coordinated agents on shared task list (compatibility facade over team) Use when this capability is needed.
metadata:
  author: sigridjineth
---

# Swarm (Compatibility Facade)

Swarm is a compatibility alias for the `/oh-my-codex:team` skill. All swarm invocations are routed to the Team skill's staged pipeline.

## Usage

```
/oh-my-codex:swarm N:agent-type "task description"
/oh-my-codex:swarm "task description"
```

## Behavior

This skill is identical to `/oh-my-codex:team`. Invoke the Team skill with the same arguments:

```
/oh-my-codex:team <arguments>
```

Follow the Team skill's full documentation for staged pipeline, agent routing, and coordination semantics.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/sigridjineth) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
