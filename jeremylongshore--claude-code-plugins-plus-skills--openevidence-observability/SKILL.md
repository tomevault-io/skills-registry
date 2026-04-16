---
name: openevidence-observability
description: | Use when this capability is needed.
metadata:
  author: jeremylongshore
---

# OpenEvidence Observability

## Key Metrics
| Metric | Alert |
|--------|-------|
| API latency p99 | > 5s |
| Error rate | > 5% |
| Daily API calls | > 80% quota |

## Logging
```typescript
async function tracked(fn: () => Promise<any>) {
  const start = Date.now();
  const result = await fn();
  logger.info({ event: 'openevidence.api', ms: Date.now() - start });
  return result;
}
```

## Resources
- [OpenEvidence Docs](https://www.openevidence.com)

## Next Steps
See `openevidence-incident-runbook`.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jeremylongshore) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
