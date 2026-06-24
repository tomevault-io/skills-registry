---
name: railway-deploy
description: Deploy this project to Railway safely with environment checks, rollout verification, and quick rollback guidance. Use when this capability is needed.
metadata:
  author: kamyil
---

## When to use

- Use for app deploys to Railway from this repository.
- Use after code changes that affect runtime behavior.

## Workflow

1. Verify status and linkage first (`railway-status` skill).
2. Validate required variables exist before deploy.
3. Trigger deploy through Railway MCP deploy tool.
4. Pull recent logs immediately after deploy.
5. Verify health endpoint/application startup signals.

## Guardrails

- Never change production-critical variables silently.
- Surface migration risk before deploy if schema changed.
- If deploy fails, capture logs and suggest minimal rollback steps.

## Output expectations

- Deployed service + environment.
- Deploy result and URL/domain.
- Post-deploy verification status and any errors.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/kamyil) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
