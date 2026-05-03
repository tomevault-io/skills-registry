---
name: next-js-stack-documentation-lookup
description: Defines the current Next.js version, its key features, and mandates checking context7 documentation before using any technology. Use when this capability is needed.
metadata:
  author: krasimir-archive
---

# Next.js Stack & Documentation Lookup

## 🔴 MANDATORY RULE: Always Check Documentation First

**Before writing or modifying ANY code involving a library or framework, you MUST:**

1. Use `mcp_context7_resolve-library-id` to find the library.
2. Use `mcp_context7_query-docs` to look up the relevant API, pattern, or feature.
3. Only then proceed with the implementation.

This applies to **every** technology in the stack, not just Next.js. Examples:
- Supabase (auth, RLS, Edge Functions)
- React (hooks, Server Components, new APIs)
- Tailwind CSS v4 (new utilities, gradient syntax)
- Framer Motion (animation APIs)
- Zustand (store patterns)
- TanStack React Query (query/mutation patterns)

> [!CAUTION]
> Never assume you know the latest API. Libraries evolve. Always verify against context7.

---

## Current Project Stack (Traumseiten Frontend)

| Technology       | Version   | Notes                                                |
|------------------|-----------|------------------------------------------------------|
| **Next.js**      | 16.2.1    | App Router, Turbopack, View Transitions enabled      |
| **React**        | 19.2.4    | Server Components, `useEffectEvent`, `Activity`      |
| **React DOM**    | 19.2.4    | Matches React version                                |
| **TypeScript**   | ^5        | Strict mode, no `any` allowed                        |
| **Tailwind CSS** | v4        | Use `bg-linear-to-*` for gradients                   |
| **Supabase**     | ^2.99.1   | Auth, RLS, SSR via `@supabase/ssr`                   |
| **Zustand**      | ^5.0.12   | Client-side state management                         |
| **React Query**  | ^5.91.2   | Server-state management                              |
| **Framer Motion**| ^12.35.1  | Animations                                           |
| **Lucide React** | ^0.577.0  | Icons                                                |

---

## Next.js 16.2 Key Features (vs 16.1.6)

### 1. View Transitions (Experimental)

- Enabled in `next.config.ts` via `experimental.viewTransition: true`.
- Uses the browser's native View Transitions API for smooth page transitions.
- Works with React's `startViewTransition`.

### 2. `updateTag` API (Read-Your-Writes)

- Import from `next/cache`.
- Use in Server Actions to expire and immediately refresh a cache tag within the same request.
- Ensures users see their changes instantly after mutations.

```ts
import { updateTag } from 'next/cache';
updateTag('user-123');
```

### 3. React 19.2 Features

- **`useEffectEvent`**: Extract non-reactive logic from Effects.
- **`Activity`**: Render components in background (display: none) while preserving state.

### 4. `after()` API (Stable)

- Import from `next/server`.
- Schedules work to execute AFTER the response is sent (logging, analytics).

```ts
import { after } from 'next/server';
after(() => { log('Page rendered'); });
```

### 5. `PageProps` Helper Type

- Type-safe access to async `params` and `searchParams`.

```ts
export default async function Page(props: PageProps<'/blog/[slug]'>) {
  const { slug } = await props.params;
}
```

### 6. Cache Components / PPR

- Enabled via `cacheComponents: true` in `next.config.ts` (replaces old `experimental_ppr`).
- Use `<Suspense>` boundaries to separate static from dynamic content.

### 7. Async `params` & `searchParams`

- In all dynamic routes, `params` and `searchParams` are now `Promise` types.
- **MUST** use `await` to access them.

### 8. Turbopack Root

- If workspace is a monorepo or non-standard layout, set `turbopack.root` in `next.config.ts`.

```ts
turbopack: {
  root: path.resolve(__dirname),
},
```

### 9. Parallel Routes Require `default.tsx`

- All parallel route slots (`@slot`) now require an explicit `default.tsx` file.

### 10. Middleware → Proxy Naming

- `skipMiddlewareUrlNormalize` is now `skipProxyUrlNormalize` in config.

---

## Upgrade Checklist (When Updating Next.js)

- [ ] Update `next`, `react`, `react-dom` to latest in `frontend/package.json`.
- [ ] Update `eslint-config-next` to match in `frontend/package.json`.
- [ ] Check for breaking changes in context7 documentation.
- [ ] Verify `turbopack.root` is set correctly.
- [ ] Run `npm run build` to catch any compile-time errors.
- [ ] Update version numbers in all current locations: the `SKILL.md` stack table, `.coderabbit.yaml`, and `frontend/package.json`.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/krasimir-archive) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
