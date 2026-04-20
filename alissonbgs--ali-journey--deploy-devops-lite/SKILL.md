---
name: deploy-devops-lite
description: Deployment and infra guidance for this repo. Use when deciding deployment strategy, environment setup, or future infra readiness (Vercel now; AWS/K8s later). Use when this capability is needed.
metadata:
  author: alissonbgs
---

# Deploy and DevOps-lite

Provide a simple deployment path now and keep the repo ready for future infra growth.

## Current deployment
- Prefer Vercel for Next.js speed and simplicity.
- Document the build command and environment variable strategy.

## Future readiness
- Prepare for introducing `apps/bff` (Node/Fastify) later.
- Keep boundaries so the web app can switch from static/content to BFF APIs via `lib/api`.

## Required logging
- Record deployment strategy choices in `ProjectDecisions.md`.
- Record infra/deploy lessons in `ProjectLearnings.md`.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/alissonbgs) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
