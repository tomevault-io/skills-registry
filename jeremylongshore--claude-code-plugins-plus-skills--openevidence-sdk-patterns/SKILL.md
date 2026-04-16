---
name: openevidence-sdk-patterns
description: | Use when this capability is needed.
metadata:
  author: jeremylongshore
---

# OpenEvidence SDK Patterns

## Singleton Client
```typescript
let instance: any = null;
export function getClient() {
  if (!instance) instance = createOpenEvidenceClient({ apiKey: process.env.OPENEVIDENCE_API_KEY });
  return instance;
}
```

## Error Wrapper
```typescript
async function safe<T>(fn: () => Promise<T>): Promise<T | null> {
  try { return await fn(); }
  catch (e: any) {
    if (e.status === 429) { await new Promise(r => setTimeout(r, 5000)); return fn(); }
    console.error('OpenEvidence error:', e.message);
    return null;
  }
}
```

## Resources
- [OpenEvidence SDK](https://www.openevidence.com)

## Next Steps
Apply in `openevidence-core-workflow-a`.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jeremylongshore) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
