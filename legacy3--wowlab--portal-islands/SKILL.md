---
name: portal-islands
description: Island architecture, PPR, Suspense boundaries, and provider placement. Read before adding providers, context, or "use client" boundaries. Use when this capability is needed.
metadata:
  author: legacy3
---

# Portal Islands & PPR

How providers, Suspense boundaries, and client boundaries work in this codebase. Read this before touching any provider, context, or island component.

## Why Islands

With `cacheComponents: true` (Next.js 16), routes are **Partially Prerendered (PPR)**. At build time, Next.js renders the component tree and extracts a **static HTML shell**. Anything that accesses uncached data (network, cookies, DB) must be either:

1. **Wrapped in `<Suspense>`**, which defers it to request time where it gets streamed in
2. **Marked with `"use cache"`**, which caches it and includes it in the static shell

If uncached data is accessed outside `<Suspense>`, the build fails with:

```
Uncached data was accessed outside of <Suspense>
```

An **island** is a `"use client"` component that bundles a provider + optional `<Suspense>` boundary. Islands are the client boundary. Everything inside renders on the client, everything outside stays in the static shell.

**Key insight:** The more content outside islands, the bigger the static shell, the faster the initial load.

## Islands in This Codebase

All islands live in `components/islands/`. No file is named "provider".

| Island         | Wraps                              | Suspense | Exports                      |
| -------------- | ---------------------------------- | -------- | ---------------------------- |
| `ReduxIsland`  | Redux `Provider` + store           | No       | —                            |
| `QueryIsland`  | `QueryClientProvider`              | No       | —                            |
| `SearchIsland` | `SearchProvider` + search entries  | No       | —                            |
| `WasmIsland`   | `WasmContext` + WASM module loader | Yes      | `useCommon()`, `useEngine()` |
| `NuqsIsland`   | `NuqsAdapter` + `Suspense`         | Yes      | —                            |

```tsx
import {
  ReduxIsland,
  QueryIsland,
  WasmIsland,
  NuqsIsland,
  SearchIsland,
} from "@/components/islands";
```

## Root Layout Nesting Order

The `[locale]/layout.tsx` nests islands in this exact order:

```
IntlayerServerProvider (server-side i18n)
  IntlayerClientProvider (client boundary for i18n)
    ThemeProvider (next-themes)
      ErrorBoundary
        ReduxIsland
          QueryIsland
            SearchIsland (receives server-fetched entries)
              WasmIsland (Suspense boundary + WASM loading)
                NodeProvider (uses useCommon() from WasmIsland)
                  AppShell (TooltipProvider + DashboardShell)
                    {children}
```

All islands are nested in ONE layout. Child layouts (like `simulate/layout.tsx`) do NOT repeat islands — they inherit from the parent.

## WasmIsland Details

WasmIsland is the most complex island:

- Outer `<Suspense>` boundary with a `WasmFallback` (renders `<EngineLoader />`)
- Inner `WasmLoader` uses `useSuspenseQuery` to load both `common` and `engine` WASM modules
- Creates a `WasmContext` with `{ common, engine }` shape
- Exports `useCommon()` and `useEngine()` hooks — both throw if used outside WasmIsland
- Uses `useSyncExternalStore` for hydration safety (mounted check)
- Has `isWasmSupported()` detection

## NuqsIsland Usage

`NuqsIsland` is NOT in the root layout. It's used at the **component level** by components that need `useQueryState()` from nuqs:

```tsx
// Wrapper+Inner pattern for nuqs
export function UrlTabs({ defaultValue, queryKey, ...props }: UrlTabsProps) {
  return (
    <NuqsIsland>
      <UrlTabsInner defaultValue={defaultValue} queryKey={queryKey} {...props} />
    </NuqsIsland>
  );
}

function UrlTabsInner({ queryKey, ... }) {
  const [value, setValue] = useQueryState(queryKey, { ... });
  // ...
}
```

Currently used by: `url-tabs.tsx`, `range-selector.tsx`.

## Auth Gates + PPR

Auth gate layouts call `supabase.auth.getUser()` which is uncached dynamic data. With cache components, they need `await connection()` from `next/server` to signal "this route is dynamic":

```tsx
// app/[locale]/simulate/layout.tsx
import { connection } from "next/server";
import { unauthorized } from "next/navigation";

export default async function SimulateLayout({ children }) {
  await connection(); // Signal: this layout is dynamic

  const supabase = await createClient();
  const {
    data: { user },
  } = await supabase.auth.getUser();
  if (!user) {
    unauthorized();
  }

  return children;
}
```

Auth gate layouts do NOT add any islands — they just guard access and pass children through.

## Route Handler Pattern

API route handlers (`app/api/`) also need `await connection()` when they access dynamic data:

```tsx
import { connection } from "next/server";

export async function GET(request: Request) {
  await connection();
  // ... dynamic logic
}
```

## How PPR Works (Reference)

Source: [Next.js docs - Cache Components / Partial Prerendering](https://nextjs.org/docs/app/building-your-application/rendering/partial-prerendering)

### Build Time

1. Next.js renders the component tree
2. Components that only do synchronous/pure work -> **static shell** (HTML)
3. Components that access uncached data -> must be in `<Suspense>` or `"use cache"`
4. Suspense fallbacks are included in the static shell

### Request Time

1. Browser receives the static shell instantly
2. Dynamic content streams in as Suspense boundaries resolve
3. Multiple Suspense boundaries resolve in parallel

### What Goes in the Static Shell

- Synchronous computations, module imports
- `"use cache"` components (cached at build, revalidated on schedule)
- Suspense fallback UI
- Server components that don't access dynamic data

### What Streams at Request Time

- Components inside `<Suspense>` that access:
  - Network requests (`fetch`, DB queries)
  - Runtime data (`cookies()`, `headers()`, `searchParams`)
  - `await connection()` (explicit dynamic signal)

### `connection()` vs `cookies()` vs `headers()`

| Function       | What It Does                        | When to Use                                                                                                                            |
| -------------- | ----------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------- |
| `cookies()`    | Reads cookies, signals dynamic      | When you need cookie data                                                                                                              |
| `headers()`    | Reads headers, signals dynamic      | When you need header data                                                                                                              |
| `connection()` | Just signals dynamic, reads nothing | When you need dynamic rendering but don't need cookies/headers (e.g., auth gates using Supabase client which reads cookies internally) |

### `"use cache"` (Not Used Yet)

For data that doesn't change often and doesn't need request context, `"use cache"` caches the result and includes it in the static shell:

```tsx
import { cacheLife } from "next/cache";

async function BlogPosts() {
  "use cache";
  cacheLife("hours");
  const posts = await fetchPosts();
  return <PostList posts={posts} />;
}
```

We don't use this yet but it's available for future optimization.

## Build Output

After `pnpm build`, check the route summary:

```
○  (Static)             fully prerendered, no dynamic content
◐  (Partial Prerender)  static shell + dynamic streaming
ƒ  (Dynamic)            fully server-rendered per request
```

All app pages should be `◐` (PPR). API routes should be `ƒ` (Dynamic).

## Adding a New Feature

### Does it use WASM (useCommon, useEngine)?

These hooks are available anywhere inside the root layout's island stack. No additional wrapping needed — `WasmIsland` is already in the root layout.

### Does it use React Query hooks?

Same — `QueryIsland` is already in the root layout. Your component just needs to be rendered inside the layout tree.

### Does it use useQueryState from nuqs?

Wrap with `NuqsIsland` at the component level using the wrapper+inner pattern (see NuqsIsland Usage section above).

### Does it need auth protection?

Add an auth gate layout with `await connection()` + `supabase.auth.getUser()` + `unauthorized()`. Do NOT add islands in the layout — they're inherited from the parent.

### Is it a pure server component?

No island needed. It becomes part of the static shell automatically.

## Never Do This

```tsx
// WRONG - repeating islands in nested layouts (they're inherited)
export default function Layout({ children }) {
  return <QueryIsland>{children}</QueryIsland>; // NO - already in root layout
}

// WRONG - provider naming (use Island suffix)
export function SomeProvider() { ... }

// WRONG - wrapping entire app in a provider
<AppProviders>{children}</AppProviders>

// WRONG - useQueryState outside NuqsIsland
export function MyComponent() {
  const [value] = useQueryState("key"); // Will crash - no NuqsAdapter
}

// WRONG - useCommon/useEngine outside WasmIsland's tree
// (but any component inside the root layout IS inside WasmIsland's tree)
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/legacy3) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
