---
name: deploy
description: Build and deploy the website to Cloudflare Pages Use when this capability is needed.
metadata:
  author: jxxcarlson
---

Run the deploy script to build and deploy the site:

```bash
sh scripts/deploy.sh
```

This will:
1. Convert any PNG/JPG images to WebP format
2. Rebuild the Haskell project
3. Regenerate the site
4. Deploy to Cloudflare Pages (jxxcarlson.org)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jxxcarlson) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
