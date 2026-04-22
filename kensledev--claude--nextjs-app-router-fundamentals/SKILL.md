---
name: nextjs-app-router-fundamentals
description: Next.js App Router (13+). Migrating from Pages, file conventions (layout, page, loading, error), routing patterns, metadata, generateStaticParams. Use when this capability is needed.
metadata:
  author: kensledev
---

# Next.js App Router Fundamentals

## Quick Start

**Files:** `layout.tsx` (required), `page.tsx` (route), `loading.tsx`, `error.tsx`,
`not-found.tsx`, `route.ts` (API)

**Pages → App:** `pages/index.tsx` → `app/page.tsx`, `pages/[id].tsx` →
`app/[id]/page.tsx`

```typescript
// app/layout.tsx (REQUIRED)
export default function RootLayout({ children }: { children: React.ReactNode }) {
  return <html lang="en"><body>{children}</body></html>;
}

// app/page.tsx
export default function Page() {
  return <h1>Home</h1>;
}

// Metadata
export const metadata = { title: 'My Page' };
```

## Reference Files

- [migration-guide.md](references/migration-guide.md) - Pages Router to App Router
  migration steps and pitfalls
- [file-conventions.md](references/file-conventions.md) - layout, page, loading,
  error, not-found, template, route files
- [routing-patterns.md](references/routing-patterns.md) - Dynamic routes,
  catch-all, route groups, parallel/intercepting routes
- [metadata-handling.md](references/metadata-handling.md) - Static and dynamic
  metadata API
- [generate-static-params.md](references/generate-static-params.md) - SSG with
  generateStaticParams

## Notes

- Root layout MUST include `<html>` and `<body>` tags
- Use `Link` from `next/link` instead of `<a>` tags
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
