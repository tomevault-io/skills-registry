---
name: next-js-16-launchpad
description: Next.js 16 with Turbopack, Cache Components, and proxy.ts. Use for bootstrapping, migrating, and building with App Router and React 19. Use when this capability is needed.
metadata:
  author: calel33
---

# Next.js 16 Launchpad

Next.js 16: Turbopack default (2-5× faster builds), Cache Components (`'use cache'`), and `proxy.ts` for explicit control.

## When to Use

✅ Next.js 16, Turbopack, Cache Components, proxy migration, App Router, React 19.2

❌ Pages Router, Next.js ≤15, generic React questions

## Requirements

| Tool | Version |
|------|---------|
| Node.js | 20.9.0+ |
| TypeScript | 5.1.0+ |
| React | 19.2+ |

## Quick Start

```bash
# New project
npx create-next-app@latest my-app

# Upgrade existing
npx @next/codemod@canary upgrade latest
npm install next@latest react@latest react-dom@latest
```

Recommended: TypeScript, ESLint, Tailwind, App Router, Turbopack, `@/*` alias.

### Minimal Setup

```tsx
// app/layout.tsx
export default function RootLayout({ children }: { children: React.ReactNode }) {
  return (
    <html lang="en">
      <body>{children}</body>
    </html>
  )
}
```

```tsx
// app/page.tsx
export default function Page() {
  return <h1>Hello, Next.js 16!</h1>
}
```

## Configuration

```ts
// next.config.ts
import type { NextConfig } from 'next'

const nextConfig: NextConfig = {
  cacheComponents: true,
  reactCompiler: true,
}

export default nextConfig
```

### v15 → v16 Changes

| v15 | v16 |
|-----|-----|
| `experimental.turbopack` | Default |
| `experimental.ppr` | `cacheComponents` |
| `middleware.ts` (Edge) | `proxy.ts` (Node) |
| Sync `params` | `await params` |

## Core Patterns

### 1. Server Components (Default)

```tsx
export default async function BlogPage() {
  const res = await fetch('https://api.example.com/posts')
  const posts = await res.json()
  return <PostList posts={posts} />
}
```

### 2. Cache Components

```tsx
import { cacheLife } from 'next/cache'

export default async function BlogPage() {
  'use cache'
  cacheLife('hours')
  const posts = await fetch('https://api.example.com/posts').then(r => r.json())
  return <PostList posts={posts} />
}
```

### 3. Client Components

```tsx
'use client'
import { useState } from 'react'

export default function Counter() {
  const [count, setCount] = useState(0)
  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={() => setCount(count + 1)}>+</button>
    </div>
  )
}
```

### 4. Proxy Boundary

```ts
// app/proxy.ts
export function proxy(request: NextRequest) {
  if (!request.cookies.get('auth') && request.nextUrl.pathname.startsWith('/dashboard')) {
    return NextResponse.redirect(new URL('/login', request.url))
  }
  return NextResponse.next()
}
```

### 5. Cache Tags + Server Actions

```tsx
// app/blog/page.tsx
'use cache'
cacheLife('hours')
cacheTag('blog-posts')

export default async function BlogList() {
  const posts = await db.posts.findMany()
  return <PostList posts={posts} />
}
```

```ts
// app/actions.ts
'use server'
import { updateTag } from 'next/cache'

export async function createPost(data: PostData) {
  await db.posts.create(data)
  updateTag('blog-posts')
}
```

### 6. Streaming with Suspense

```tsx
export default function Dashboard() {
  return (
    <div>
      <Suspense fallback={<Skeleton />}>
        <RevenueCard />
      </Suspense>
      <Suspense fallback={<Skeleton />}>
        <UsersCard />
      </Suspense>
    </div>
  )
}

async function RevenueCard() {
  const data = await db.analytics.revenue()
  return <div>{data}</div>
}
```

## Key Concepts

1. **Turbopack** - Rust bundler, incremental compilation, Fast Refresh
2. **Server Components** - Default in `app/`, zero client JS
3. **Client Components** - `'use client'`, hooks, browser APIs
4. **Cache Components** - `'use cache'` + `cacheLife()` for PPR
5. **Proxy Boundary** - `proxy.ts` for auth/rewrites/redirects
6. **Partial Pre-Rendering** - Static shell + dynamic streaming

## Migration Checklist

1. **Async Request APIs**
   ```bash
   npx @next/codemod@canary async-request-api
   ```
   Update: `const { slug } = await params`

2. **middleware.ts → proxy.ts**
   - Rename file, export `proxy`
   - Node runtime only (not Edge)

3. **Config updates**
   - Remove `experimental.*` flags
   - Enable `cacheComponents`, `reactCompiler`
   - Remove `serverRuntimeConfig`/`publicRuntimeConfig`

4. **Cache Components**
   - Replace `experimental.ppr` with `cacheComponents: true`
   - Wrap dynamic sections with `<Suspense>`

5. **Images**
   - Configure `images.localPatterns` for query strings

See `references/nextjs16-migration-playbook.md` for complete guide.

## Common Pitfalls

❌ Mixing `'use cache'` with runtime APIs (`cookies()`, `headers()`)
❌ Missing `<Suspense>` when Cache Components enabled
❌ Tilde Sass imports under Turbopack
❌ Running `proxy.ts` on Edge runtime

✅ Read cookies/headers first, pass as props to cached components
✅ Wrap dynamic children in `<Suspense>`
✅ Use standard Sass imports
✅ Use Node runtime for proxy

## Decision Guide

**Enable Cache Components?** 
→ Yes for static/semi-static content
→ No for fully dynamic dashboards

**Where does auth live?**
→ `proxy.ts` for cross-route checks
→ Route handlers for API-specific logic

**When to use `'use client'`?**
→ Only when you need hooks, state, or browser APIs
→ Keep presentational components server-side

## Production Patterns

### E-commerce
```tsx
// Product page with streaming
export default async function Product({ params }) {
  const { id } = await params
  const product = await db.products.findById(id)
  
  return (
    <>
      <ProductInfo product={product} />
      <Suspense fallback={<ReviewsSkeleton />}>
        <Reviews productId={id} />
      </Suspense>
    </>
  )
}
```

### Authenticated Dashboard
```ts
// proxy.ts
export function proxy(request: NextRequest) {
  const session = request.cookies.get('session')
  if (!session && request.nextUrl.pathname.startsWith('/dashboard')) {
    return NextResponse.redirect(new URL('/login', request.url))
  }
}
```

See `references/nextjs16-advanced-patterns.md` for more blueprints.

## Performance

- Keep Turbopack enabled (opt-out with `--webpack` only if needed)
- Parallelize fetches with `Promise.all`
- Use `<Suspense>` for streaming boundaries
- Enable file system cache for large repos

## Security

- Use `server-only` package + React Taint API
- Keep auth in `proxy.ts`
- Validate inputs in Server Actions
- Gate env vars with `NEXT_PUBLIC_` prefix
- Extract cookies/headers before cached scopes

## Deployment

- Vercel: Zero-config
- Docker/Node: `output: 'standalone'`
- Monitor build times (2-5× speedup expected)
- Configure cache lifecycles to match CDN

## Reference Files

- **references/nextjs16-reference.md** - Install/config/checklists
- **references/nextjs16-migration-playbook.md** - Migration guide with codemods
- **references/nextjs16-advanced-patterns.md** - Streaming, caching, auth patterns
- **references/NEXTJS_16_COMPLETE_GUIDE.md** - Complete documentation
- **scripts/bootstrap-nextjs16.ps1** - Automated setup script
- **assets/app-router-starter/** - Reference implementation

## Resources

- Docs: https://nextjs.org/docs
- GitHub: https://github.com/vercel/next.js

**Version:** 1.1.0 | **Updated:** 2025-12-27

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/calel33) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
