---
name: juicebox-prod-checklist
description: | Use when this capability is needed.
metadata:
  author: jeremylongshore
---

# Juicebox Production Checklist

## Checklist
- [ ] Production API key in secret manager
- [ ] Rate limiting per plan tier
- [ ] Error handling (401, 403, 429, 500)
- [ ] Candidate data encrypted at rest
- [ ] GDPR/CCPA retention policy
- [ ] Health check tests connectivity
- [ ] Quota usage monitoring
- [ ] Outreach templates compliance-reviewed

## Health Check
```typescript
async function health() {
  try {
    await client.search({ query: 'test', limit: 1 });
    return { status: 'healthy' };
  } catch { return { status: 'degraded' }; }
}
```

## Resources
- [Status](https://status.juicebox.ai)

## Next Steps
See `juicebox-upgrade-migration`.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jeremylongshore) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
