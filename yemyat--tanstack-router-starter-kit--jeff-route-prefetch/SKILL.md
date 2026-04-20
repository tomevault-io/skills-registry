---
name: jeff-route-prefetch
description: Jeff's route prefetch conventions for TanStack Router + React Query. Use when implementing or refactoring any data-fetching route, loader, or hooks. Use when this capability is needed.
metadata:
  author: yemyat
---

# Jeff's Route Prefetch

Use this skill for route data preloading so first render uses warm cache.

## Apply when

- Adding/refactoring a route that fetches server data.
- Moving inline route data fetching into hook files.
- Implementing loader prefetch for table/detail pages.

## Required pattern

1. In hook files, export a standalone `queryOptions` factory.
2. Keep `useQuery` wrapper separate and consume that factory.
3. In route `loader`, call `context.queryClient.ensureQueryData(...)` for initial queries.
4. Do not `await` those `ensureQueryData` calls; fire-and-forget for parallelism.
5. Loader default params must match component initial URL/UI state exactly.

## Hook file guidance

- `queryOptions` functions must be pure (no React hooks inside).
- Keep `select` in `useQuery(...)`, not inside `queryOptions`.
- Keep pagination constants shared, not route-local duplicates.
- Shared data hooks belong in `src/lib/hooks/{module}/...`.

## Route file guidance

- Route files define route config, loader wiring, and UI composition.
- Avoid inline `useQuery`/`useMutation` logic in route pages; prefer hook modules.
- Preload all first-render dependencies in loader, not just the primary table query.

## Validation checklist

- [ ] Initial navigation avoids flash-of-skeleton for preloaded datasets.
- [ ] Loader defaults and URL defaults are aligned.
- [ ] Query keys include all fetch-affecting params.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/yemyat) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
