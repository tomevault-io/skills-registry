---
name: create-page
description: Create a new page using Next.js 15 App Router conventions Use when this capability is needed.
metadata:
  author: nismit
---

# Create Page: $ARGUMENTS

## Directory Structure

```
src/app/[route]/
├── page.tsx         # Page component (Server Component by default)
├── layout.tsx       # Shared layout (optional, preserves state)
├── template.tsx     # Re-rendered layout (optional, resets state)
├── loading.tsx      # Loading UI with Suspense (optional)
├── error.tsx        # Error boundary (optional, requires 'use client')
└── not-found.tsx    # 404 page (optional)
```

## Static Page Template

```tsx
// src/app/[route]/page.tsx
import type { Metadata } from 'next';

export const metadata: Metadata = {
  title: 'Page Title',
  description: 'Page description',
};

export default function PageName() {
  return (
    <main>
      {/* Page content */}
    </main>
  );
}
```

## Dynamic Page Template (Next.js 15)

**IMPORTANT: In Next.js 15, `params` is a Promise and must be awaited.**

```tsx
// src/app/[route]/[slug]/page.tsx
import type { Metadata } from 'next';

type Props = {
  params: Promise<{ slug: string }>;
};

// Generate static paths at build time
export async function generateStaticParams() {
  const items = await fetchItems();
  return items.map((item) => ({
    slug: item.slug,
  }));
}

// Dynamic metadata
export async function generateMetadata({ params }: Props): Promise<Metadata> {
  const { slug } = await params;
  const item = await fetchItem(slug);

  return {
    title: item.title,
    description: item.description,
  };
}

// Page component
export default async function PageName({ params }: Props) {
  const { slug } = await params;
  const item = await fetchItem(slug);

  return (
    <main>
      <h1>{item.title}</h1>
      {/* Page content */}
    </main>
  );
}
```

## Catch-all Routes

```tsx
// src/app/docs/[...slug]/page.tsx - Required catch-all
// src/app/docs/[[...slug]]/page.tsx - Optional catch-all

type Props = {
  params: Promise<{ slug: string[] }>;
};

export async function generateStaticParams() {
  return [
    { slug: ['getting-started'] },
    { slug: ['api', 'components'] },
    { slug: ['api', 'components', 'link'] },
  ];
}

export default async function DocsPage({ params }: Props) {
  const { slug } = await params;
  const path = slug?.join('/') || 'index';

  return <div>Docs: {path}</div>;
}
```

## Route Groups

```
src/app/
├── (marketing)/      # URL: /about, /contact
│   ├── about/
│   │   └── page.tsx
│   └── contact/
│       └── page.tsx
├── (dashboard)/      # URL: /settings, /profile
│   ├── layout.tsx    # Shared dashboard layout
│   ├── settings/
│   │   └── page.tsx
│   └── profile/
│       └── page.tsx
└── page.tsx          # URL: /
```

## Error Handling

```tsx
// src/app/[route]/error.tsx
'use client';

type Props = {
  error: Error & { digest?: string };
  reset: () => void;
};

export default function Error({ error, reset }: Props) {
  return (
    <div>
      <h2>Something went wrong</h2>
      <p>{error.message}</p>
      <button onClick={() => reset()}>Try again</button>
    </div>
  );
}
```

## Loading UI

```tsx
// src/app/[route]/loading.tsx
export default function Loading() {
  return <div>Loading...</div>;
}
```

## SSG Configuration

For static export:

1. All dynamic routes must have `generateStaticParams`
2. No server-only features at runtime (cookies, headers)
3. Set `output: 'export'` in `next.config.ts` for full static export

```ts
// next.config.ts
import type { NextConfig } from 'next';

const config: NextConfig = {
  output: 'export',
};

export default config;
```

## Checklist

- [ ] Create page.tsx with proper types
- [ ] Add metadata (static or dynamic)
- [ ] Add generateStaticParams for dynamic routes
- [ ] Add loading.tsx for async pages
- [ ] Add error.tsx for error handling
- [ ] Verify static generation with `npm run build`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/nismit) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
