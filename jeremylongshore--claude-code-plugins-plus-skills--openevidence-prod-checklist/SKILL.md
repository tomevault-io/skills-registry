---
name: openevidence-prod-checklist
description: | Use when this capability is needed.
metadata:
  author: jeremylongshore
---

# OpenEvidence Production Checklist

## Pre-Launch
- [ ] Production credentials in secret manager
- [ ] Rate limiting implemented
- [ ] Error handling for all API codes
- [ ] Health check endpoint
- [ ] Monitoring and alerting
- [ ] Rollback procedure documented

## Health Check
```typescript
async function health() {
  try { /* test OpenEvidence API call */ return { status: 'healthy' }; }
  catch { return { status: 'degraded' }; }
}
```

## Resources
- [OpenEvidence Docs](https://www.openevidence.com)

## Next Steps
See `openevidence-upgrade-migration`.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jeremylongshore) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
