---
name: cloudflare-deploy
description: Deploy the resume app to Cloudflare Pages using the documented build and wrangler steps. Use when deploying, troubleshooting deploys, or updating Pages-related CI configuration. Use when this capability is needed.
metadata:
  author: salieri
---

# Cloudflare Deploy

## Overview
Build and deploy the resume app to Cloudflare Pages using the project workflow.

## Workflow
1. Build the app: `pnpm --workspace-root build` (or `cd dev/apps/resume && pnpm build`).
2. Deploy from `dev/apps/resume`: `wrangler pages deploy ./dist/client --project-name resume --branch main`.
3. Provide `CLOUDFLARE_API_TOKEN` and `CLOUDFLARE_ACCOUNT_ID` environment variables.
4. Review `.github/workflows/deploy-release.yml` when adjusting CI deploy behavior.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/salieri) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
