---
name: juicebox-local-dev-loop
description: | Use when this capability is needed.
metadata:
  author: jeremylongshore
---

# Juicebox Local Dev Loop

## Project Structure
```
my-juicebox-app/
├── .env                    # JUICEBOX_API_KEY=jb_live_...
├── src/client.ts           # Singleton
├── src/searches/           # Query definitions
├── tests/fixtures/         # Mock results
└── scripts/dev.ts
```

## Mock Data
```typescript
export const mockSearch = {
  total: 150,
  profiles: [{ id: 'prof_1', name: 'Jane Smith', title: 'Engineer', company: 'Google' }]
};
```

## Cost Control
```typescript
const limit = process.env.NODE_ENV === 'development' ? 5 : 50;
const results = await client.search({ query, limit });
```

## Resources
- [Juicebox Docs](https://docs.juicebox.work)

## Next Steps
See `juicebox-sdk-patterns`.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jeremylongshore) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
