---
name: validate-docs
description: Validate documentation by spawning a fresh sub-agent to analyze the repository and surface ambiguities. Use after updating docs, before clearing context. Use when this capability is needed.
metadata:
  author: j-d0g
---

# Validate Docs

Test if documentation is self-sufficient by having a fresh agent try to understand the project.

## Usage

Spawn the `doc-validator` agent in background (`run_in_background: true`).

The agent will return an output file path. Use `Read` or `tail` to check results when ready.

## Triage Ambiguities

For each ambiguity reported, the supervisor judges:

**Valid → Fix**
- Genuinely missing information
- Could cause mistakes or block progress
- Propose specific fix to user

**Dismiss**
- Out of scope for current phase
- Over-engineered concern
- Agent will figure it out during execution

Present each ambiguity with your judgment. User decides whether to fix or dismiss.

## After Fixes

Re-run validation to confirm ambiguities are resolved.

## Success

When no blocking ambiguities remain:

```
Documentation validated. Safe to clear context.
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/j-d0g) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
