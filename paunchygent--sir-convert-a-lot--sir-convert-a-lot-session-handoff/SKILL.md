---
name: sir-convert-a-lot-session-handoff
description: >- Use when this capability is needed.
metadata:
  author: paunchygent
---

# Sir Convert-a-Lot Session Handoff

## Use This Skill When

- A task phase ends and handoff context must be recorded.
- A new contributor/agent needs an exact next-step instruction pack.
- Review findings, blockers, and pending validations need durable capture.

## Canonical Files To Update

- `.agents/session/readme-first.md`
- `.agents/session/handoff.md`
- `docs/backlog/current.md`

## Handoff Requirements

1. Record what changed and why (link file paths and doc IDs).
1. Record validation outcomes with command surfaces used.
1. Record open risks, decisions pending, and the immediate next executable step.
1. Keep wording implementation-focused; avoid ambiguous "continue here" notes.

## Required Validation Block

Include explicit pass/fail status for:

```bash
pdm run validate-tasks
pdm run validate-docs
```

If code changed, also include:

```bash
pdm run format-all
pdm run lint-fix
pdm run typecheck-all
pdm run pytest-root <path-or-nodeid>
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/paunchygent) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
