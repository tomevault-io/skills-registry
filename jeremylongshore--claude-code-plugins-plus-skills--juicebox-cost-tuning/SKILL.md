---
name: juicebox-cost-tuning
description: | Use when this capability is needed.
metadata:
  author: jeremylongshore
---

# Juicebox Cost Tuning

## Cost Factors
| Feature | Cost Driver |
|---------|-------------|
| Search | Per query |
| Enrichment | Per profile |
| Contact data | Per lookup |
| Outreach | Per message |

## Reduction Strategies
1. Cache search results (avoid duplicate queries)
2. Use filters (fewer wasted enrichments)
3. Only enrich top-scored candidates
4. Only get contacts for final candidates

## Quota Monitoring
```typescript
const quota = await client.account.getQuota();
console.log(`Searches: ${quota.searches.used}/${quota.searches.limit}`);
if (quota.searches.used > quota.searches.limit * 0.8) console.warn('80% quota used');
```

## Resources
- [Pricing](https://juicebox.ai/pricing)

## Next Steps
See `juicebox-reference-architecture`.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jeremylongshore) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
