---
name: railway-status
description: Check Railway CLI auth, linked project/service, and current deployment health before making changes. Use when this capability is needed.
metadata:
  author: kamyil
---

## When to use

- Use this first for any Railway task in this repo.
- Confirm CLI auth, project linkage, environment linkage, and basic service status.

## Workflow

1. Run Railway MCP status tools first (`check-railway-status`, `list-projects`, `list-services`).
2. Confirm current linked project/service/environment before deploy actions.
3. If project is not linked, link it before proceeding.
4. For failed checks, stop and report exact failure and next command.

## Output expectations

- Return linked project/service/environment names.
- Return whether auth is valid.
- Return a short action plan for deploy/debug.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/kamyil) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
