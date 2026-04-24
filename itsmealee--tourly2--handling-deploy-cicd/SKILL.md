---
name: handling-deploy-cicd
description: Deployment protocols for Vercel/Appwrite. Use for managing production builds and environment variables. Use when this capability is needed.
metadata:
  author: itsmealee
---

# CI/CD and Deployment

## When to use this skill
- When pushing to `main`.
- When setting up a new environment.

## Deployment Checklist
- [ ] Environment Variables: Ensure all `.env` keys exist in Vercel/Appwrite.
- [ ] Build Check: Run `npm run build` locally first to catch errors.
- [ ] Platforms: Whitelist Vercel domain in Appwrite "Settings > Platforms".
- [ ] Webhooks: Ensure the production Webhook URL is correct.

## Instructions
- **Preview Deploys**: Use branch previews to test features before merging to `main`.
- **Logs**: Monitor Vercel "Runtime Logs" immediately after a major deploy.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/itsmealee) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
