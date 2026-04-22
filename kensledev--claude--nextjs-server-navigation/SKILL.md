---
name: nextjs-server-navigation
description: Server Component navigation: <Link> for links, redirect() for conditional redirects. NO 'use client' needed for static links or server redirects. Use when this capability is needed.
metadata:
  author: kensledev
---

# Next.js: Server Component Navigation

## Quick Start

**Server Components use DIFFERENT methods than Client Components!**

| Method | Server Component | Client Component |
|--------|-----------------|------------------|
| Links | `<Link>` ✅ | `<Link>` ✅ |
| Redirect | `redirect()` ✅ | ❌ No |
| Router hook | ❌ No | `useRouter()` ✅ |

```typescript
// app/page.tsx - Server Component (NO 'use client'!)
import Link from 'next/link';

export default async function Page() {
  const data = await fetchData();  // Can fetch!

  return (
    <div>
      <h1>Welcome</h1>
      <Link href="/dashboard">Dashboard</Link>
      <Link href={`/products/${data.id}`}>View Product</Link>
    </div>
  );
}
```

## Conditional Redirect

```typescript
import { redirect } from 'next/navigation';

export default async function ProfilePage() {
  const session = await getSession();
  if (!session) redirect('/login');  // Server-side redirect
  return <div>Welcome</div>;
}
```

## ❌ WRONG - Adding 'use client' for Navigation

```typescript
// ❌ WRONG
'use client';  // Don't add this just for links!
export default function Page() {
  return <Link href="/about">About</Link>;
}
```

## ✅ CORRECT - Keep as Server Component

```typescript
// ✅ CORRECT
import Link from 'next/link';
export default async function Page() {
  return <Link href="/about">About</Link>;
}
```

## Notes

- **No 'use client' needed:** `<Link>` and `redirect()` work in Server Components
- **useRouter() is client-only:** Only for Client Components
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
