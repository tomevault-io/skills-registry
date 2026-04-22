---
name: nextjs-client-cookie-pattern
description: Client component onClick/form → Server Action → set cookie. 'use client' calls 'use server' action using cookies() from next/headers. Use when this capability is needed.
metadata:
  author: kensledev
---

# Next.js: Client → Server Cookie Pattern

## Quick Start

**Two files:** Client component (`'use client'`) + Server action (`'use server'`)

```typescript
// Button.tsx
'use client';
import { setTheme } from './actions';
export default function Button() {
  return <button onClick={() => setTheme('dark')}>Enable Dark Mode</button>;
}

// actions.ts
'use server';
import { cookies } from 'next/headers';
export async function setTheme(theme: string) {
  const cookieStore = await cookies();
  cookieStore.set('theme', theme, { httpOnly: true, maxAge: 60*60*24*365 });
}
```

## Reference Files

- [patterns.md](references/patterns.md) - Cookie patterns, form submission,
  redirect, reading cookies

## Notes

- **Server-side only:** `cookies()` only works in Server Actions/Components
- **Use document.cookie** in client components to read
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
