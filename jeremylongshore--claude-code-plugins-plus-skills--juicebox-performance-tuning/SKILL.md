---
name: juicebox-performance-tuning
description: | Use when this capability is needed.
metadata:
  author: jeremylongshore
---

# Juicebox Performance Tuning

## Use Specific Filters
```typescript
// SLOW: broad query
await client.search({ query: 'engineer' });
// FAST: targeted with filters
await client.search({ query: 'backend engineer', filters: { location: 'SF', skills: ['Go'] }, limit: 20 });
```

## Cache Results
```typescript
const cache = new Map();
async function cachedSearch(query: string) {
  const cached = cache.get(query);
  if (cached?.expiry > Date.now()) return cached.result;
  const result = await client.search({ query, limit: 20 });
  cache.set(query, { result, expiry: Date.now() + 300_000 });
  return result;
}
```

## Batch Enrichment
```typescript
const enriched = await client.enrichBatch({
  profile_ids: profiles.map(p => p.id),
  fields: ['skills_map', 'contact']
});
```

## Resources
- [Performance](https://docs.juicebox.work/performance)

## Next Steps
See `juicebox-cost-tuning`.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jeremylongshore) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
