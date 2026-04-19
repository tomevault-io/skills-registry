---
name: deploy
description: Deploy the application to production. Use when user says deploy, push to production, or update live site. Use when this capability is needed.
metadata:
  author: forever-efficient
---

# Deploy

Execute deployment following this sequence:

## Pre-deployment Checks
1. Verify tests pass: `{{TEST_COMMAND}}`
2. Check for uncommitted changes: `git status`

## Build
1. Build the application: `{{BUILD_COMMAND}}`
2. Verify build output exists

## Deploy
1. {{DEPLOY_STEP_1}}
2. {{DEPLOY_STEP_2}}

## Post-deployment
1. Verify site is accessible
2. Run smoke tests if available

## Rollback
If deployment fails:
1. {{ROLLBACK_STEP_1}}
2. {{ROLLBACK_STEP_2}}

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/forever-efficient) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
