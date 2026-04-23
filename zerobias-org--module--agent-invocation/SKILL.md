---
name: agent-invocation
description: Agent invocation syntax and boundary rules Use when this capability is needed.
metadata:
  author: zerobias-org
---

# Agent Invocation Rules

## Invocation Syntax

Use `@agent-name` followed by the task:
```
@api-researcher Research GitHub webhooks
@api-architect Design the OpenAPI spec
```

## Agent Boundaries

- Each agent has EXCLUSIVE responsibilities
- NO overlap between agents allowed
- Check agent descriptions for triggers
- Respect "does NOT" boundaries

## Context Passing

When passing context between agents:
1. Save output to memory folder
2. Reference previous phase outputs
3. Include all necessary context
4. Use agent-parameter-passing skill

## Agent Loading

Each agent must:
1. Load its required rules
2. Assert its responsibilities
3. Check for prerequisites
4. Validate inputs

## Collaboration Rules

- Agents work sequentially, not in parallel
- Each agent completes before next starts
- Use memory files for handoffs
- Document decisions made

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/zerobias-org) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
