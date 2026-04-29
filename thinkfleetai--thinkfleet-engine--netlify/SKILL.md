---
name: netlify
description: Manage Netlify sites, deploys, and functions via API. Deploy and configure web projects. Use when this capability is needed.
metadata:
  author: thinkfleetai
---
# Netlify
Web deployment platform.
## Environment
```bash
export NETLIFY_AUTH_TOKEN="xxxxxxxxxx"
```
## CLI Commands
```bash
netlify sites:list
netlify deploy --prod
netlify env:list
netlify functions:list
```
## API - List Sites
```bash
curl "https://api.netlify.com/api/v1/sites" -H "Authorization: Bearer $NETLIFY_AUTH_TOKEN"
```
## API - Trigger Deploy
```bash
curl -X POST "https://api.netlify.com/api/v1/sites/{site_id}/builds" \
  -H "Authorization: Bearer $NETLIFY_AUTH_TOKEN"
```
## API - List Deploys
```bash
curl "https://api.netlify.com/api/v1/sites/{site_id}/deploys" \
  -H "Authorization: Bearer $NETLIFY_AUTH_TOKEN"
```
## Links
- Dashboard: https://app.netlify.com
- Docs: https://docs.netlify.com/api/get-started/

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/thinkfleetai) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
