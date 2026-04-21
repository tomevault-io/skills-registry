---
name: deploy
description: Build and deploy the Docusaurus site to Vercel production Use when this capability is needed.
metadata:
  author: ngtanthanh-qc
---

# Deploy to Vercel

Deploy the Docusaurus site to production on Vercel.

## Pre-deployment Checklist

1. **Ensure clean build** by running:
   ```bash
   npm run build:production
   ```

2. **Check git status** - ensure all changes are committed:
   ```bash
   git status
   ```

3. If there are uncommitted changes, warn the user and ask whether to proceed.

## Deployment

The site is deployed to Vercel. Deployment typically happens automatically via Git push:

```bash
git push origin main
```

Vercel is configured with:
- Build command: `npm run build:production`
- Output directory: `build`
- Node.js >= 20.0

## Post-deployment

- Verify the deployment at: https://docs.tanthanh.dev
- Check Vercel dashboard for build status
- Report any deployment errors

## Important Notes

- NEVER force push to main
- Always verify the build succeeds locally before pushing
- Check that environment variables are set in Vercel dashboard if Firebase auth is needed

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ngtanthanh-qc) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
