---
name: openevidence-local-dev-loop
description: | Use when this capability is needed.
metadata:
  author: jeremylongshore
---

# OpenEvidence Local Dev Loop

## Project Structure
```
my-openevidence-app/
├── .env                # OPENEVIDENCE_API_KEY=...
├── src/client.ts       # Singleton
├── tests/fixtures/     # Mock responses
└── scripts/dev.ts
```

## Mock Data
```typescript
export const mockResponse = {
  status: 'success',
  data: { /* mock OpenEvidence response */ }
};
```

## Dev Script
```json
{ "scripts": { "dev": "tsx watch src/index.ts", "test": "vitest" } }
```

## Resources
- [OpenEvidence Docs](https://www.openevidence.com)

## Next Steps
See `openevidence-sdk-patterns`.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jeremylongshore) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
