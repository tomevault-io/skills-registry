---
name: deploy
description: Deploy the cloud worker to Railway. Builds and pushes the latest code. Use when this capability is needed.
metadata:
  author: etanhey
---

# Deploy to Railway

Deploy the golems cloud worker to Railway.

## Process

1. Verify we're on a clean git state (no uncommitted changes)
2. Run tests: `bun test` — must pass
3. Push current branch to remote
4. Trigger Railway deployment: `railway up` or git-based deploy
5. Monitor deployment logs: `railway logs --follow`
6. Verify health endpoint: `curl https://golems-production.up.railway.app/`
7. Report deployment status

## Environment

- **Railway URL**: `golems-production.up.railway.app`
- **Entry point**: `packages/services/src/cloud-worker.ts`
- **Required env vars**: `ANTHROPIC_API_KEY`, `SUPABASE_URL`, `SUPABASE_SERVICE_KEY`, `TELEGRAM_*`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/etanhey) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
