---
name: juicebox-rate-limits
description: | Use when this capability is needed.
metadata:
  author: jeremylongshore
---

# Juicebox Rate Limits

## Rate Limits by Plan
| Plan | Searches/min | Enrichments/min | Contacts/day |
|------|-------------|-----------------|---------------|
| Starter | 10 | 5 | 100 |
| Professional | 60 | 30 | 1,000 |
| Enterprise | 300 | 100 | 10,000 |

## Implementation
```typescript
import PQueue from 'p-queue';
const queue = new PQueue({ concurrency: 5, interval: 60_000, intervalCap: 60 });

async function rateLimitedSearch(query: string) {
  return queue.add(() => client.search({ query, limit: 10 }));
}
```

## Resources
- [Rate Limits](https://docs.juicebox.work/rate-limits)

## Next Steps
See `juicebox-security-basics`.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jeremylongshore) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
