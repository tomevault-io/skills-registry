---
name: deploy-vercel
description: Deploy web applications to Vercel. Use when the user wants to deploy, publish, or ship a web app to Vercel hosting. Use when this capability is needed.
metadata:
  author: skill-tools
---

# Deploy to Vercel

Deploy your web application to Vercel with zero configuration.

## Prerequisites

- Vercel CLI installed (`npm i -g vercel`)
- Authenticated with `vercel login`

## Steps

1. Run `vercel` in the project root
2. Follow the prompts to link your project
3. Verify deployment at the provided URL

## Error Handling

- **Authentication failed**: Run `vercel login` again
- **Build failed**: Check the build logs with `vercel logs`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/skill-tools) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
