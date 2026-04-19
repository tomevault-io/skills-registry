---
name: deploy-functions
description: Build and deploy Firebase Functions to production Use when this capability is needed.
metadata:
  author: blanxlait
---

Deploy the Firebase Functions:

1. Build functions: `npm --prefix functions run build`
2. Deploy: `firebase deploy --only functions`
3. Verify deployment by checking function URLs in output

If deployment fails with rate limiting, wait 60 seconds and retry.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/blanxlait) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
