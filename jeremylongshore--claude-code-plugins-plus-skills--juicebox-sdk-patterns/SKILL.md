---
name: juicebox-sdk-patterns
description: | Use when this capability is needed.
metadata:
  author: jeremylongshore
---

# Juicebox SDK Patterns

## Singleton Client
```typescript
let instance: JuiceboxClient | null = null;
export function getClient(): JuiceboxClient {
  if (!instance) instance = new JuiceboxClient({ apiKey: process.env.JUICEBOX_API_KEY });
  return instance;
}
```

## Batch Search with Dedup
```typescript
async function batchSearch(queries: string[]): Promise<Profile[]> {
  const seen = new Set<string>();
  const all: Profile[] = [];
  for (const q of queries) {
    const r = await client.search({ query: q, limit: 20 });
    for (const p of r.profiles) {
      if (!seen.has(p.linkedin_url)) { seen.add(p.linkedin_url); all.push(p); }
    }
  }
  return all;
}
```

## Error Wrapper
```typescript
async function safeCall<T>(fn: () => Promise<T>): Promise<T | null> {
  try { return await fn(); }
  catch (e: any) {
    if (e.status === 429) { await new Promise(r => setTimeout(r, 5000)); return fn(); }
    return null;
  }
}
```

## Resources
- [SDK Reference](https://docs.juicebox.work/sdk)

## Next Steps
Apply in `juicebox-core-workflow-a`.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jeremylongshore) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
