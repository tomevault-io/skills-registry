---
name: deploy
description: Deploy application with safety checks and rollback guidance Use when this capability is needed.
metadata:
  author: finger-gun
---

# Deployment

Use this skill to execute a safe, repeatable deployment.

## Pre-deploy

1. Run tests and typechecks
2. Confirm environment variables
3. Build artifacts
4. Review recent changes

## Deploy Steps

1. Run build pipeline
2. Deploy artifacts
3. Run post-deploy health checks
4. Verify logs and metrics

## Rollback

If deployment fails, use rollback procedure in `resources/rollback.md`.

## Resources

- `resources/deploy-checklist.md`
- `resources/rollback.md`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/finger-gun) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
