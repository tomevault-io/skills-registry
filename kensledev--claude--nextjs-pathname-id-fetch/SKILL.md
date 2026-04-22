---
name: nextjs-pathname-id-fetch
description: Fetch data using URL parameter. Create app/[id]/page.tsx, await params to get ID, fetch API, render. Server Component (no 'use client'). Use when this capability is needed.
metadata:
  author: kensledev
---

# Next.js: Pathname ID Fetch Pattern

## Quick Start

**Dynamic route folder:** `app/[id]/page.tsx` → URL segment accessible as `params`

```typescript
// app/[id]/page.tsx
export default async function ProductPage({
  params,
}: {
  params: Promise<{ id: string }>;
}) {
  const { id } = await params;  // Next.js 15+: await params
  const product = await fetch(`https://api.example.com/products/${id}`)
    .then(r => r.json());
  return <div><h1>{product.name}</h1></div>;
}
```

## Reference Files

- [pattern.md](references/pattern.md) - Full implementation, parameter names,
  multiple parameters

## Notes

- **Server Component (default):** No 'use client' needed
- **Keep structure simple:** Use `app/[id]/` unless explicitly told otherwise
- **Last verified:** 2025-01-11

<!--
PROGRESSIVE DISCLOSURE GUIDELINES:
- Keep this file ~50 lines total (max ~150 lines)
- Use 1-2 code blocks only (recommend 1)
- Keep description <200 chars for Level 1 efficiency
- Move detailed docs to references/ for Level 3 loading
- This is Level 2 - quick reference ONLY, not a manual
-->

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/kensledev) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
