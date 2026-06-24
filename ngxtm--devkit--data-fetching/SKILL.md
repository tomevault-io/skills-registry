---
name: next-js-data-fetching
description: Fetch API, Caching, and Revalidation strategies. Use when this capability is needed.
metadata:
  author: ngxtm
---

# Data Fetching (App Router)

## **Priority: P0 (CRITICAL)**

Fetch data directly in Server Components using `async/await`.

## Fetch API Extensions

Next.js extends the native `fetch` API for granular caching control.

- **Static (Default)**: `fetch('https://api.com')` -> `cache: 'force-cache'`. Built at build time.
- **Dynamic**: `fetch('...', { cache: 'no-store' })`. Fetched on every request.
- **Revalidated (ISR)**: `fetch('...', { next: { revalidate: 60 } })`. Cached for 60s.

## Patterns

- **Colocation**: Fetch data where it's used. Next.js automatically deduplicates requests for the same URL in the same render pass.
- **Parallel**: Use `Promise.all()` to prevent waterfalls.

  ```tsx
  const [user, posts] = await Promise.all([getUser(), getPosts()]);
  ```

- **Blocking**: To prevent UI blocking, wrap the component in `<Suspense>` and stream the result.

## Revalidation

- **Path**: `revalidatePath('/blog/[slug]')` - Purges cache for specific route.
- **Tag**: `revalidateTag('collection')` - Purges all fetches tagged with `next: { tags: ['collection'] }`.

## Client-Side Fetching (Live Data)

Server Components are the default, but for live/user-specific data that doesn't need SEO:

- **SWR / TanStack Query**: PREFERRED over `useEffect`. Handles caching, polling, and deduplication automatically.

  ```tsx
  'use client';
  import useSWR from 'swr';

  // Good: "Stale-While-Revalidate" - Fast UI, then updates
  const { data } = useSWR('/api/user', fetcher);
  ```

- **Anti-Pattern**: Do not use `useEffect` to fetch data if you can avoid it. It causes "Flash of Loading Content" and waterfalls.

## Anti-Patterns

- **API Routes**: Don't fetch your own API Routes (`/api/...`) from Server Components. Call the DB/Service function directly.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ngxtm) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
