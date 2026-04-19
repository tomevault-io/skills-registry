---
name: nuxt-skills
description: Follow Nuxt 3/4 conventions for file structure, composables, data fetching, and routing. Use when building or editing Nuxt apps, pages, layouts, middleware, or nuxt.config. Use when this capability is needed.
metadata:
  author: yulei-chen
---

# Nuxt Skills

## Project layout

- **Source directory**: Prefer `srcDir: 'app'` so `app/` holds pages, components, composables, layouts, middleware. Reference as `~/` (e.g. `~/components/`, `~/composables/`).
- **Pages**: `app/pages/*.vue` — file-based routing. Use `definePageMeta({ middleware: 'auth' })` for route-level middleware.
- **Layouts**: `app/layouts/default.vue` (or named). Use `<NuxtLayout>` or `app.vue` to wrap content.
- **Components**: `app/components/` — auto-imported; use PascalCase names in templates.

## Composables

- **Location**: `app/composables/*.ts` — auto-imported. Export functions named `useXxx`.
- **Pattern**: Accept `Ref<T> | T` for flexibility; normalize with `typeof x === 'string' ? ref(x) : x` when needed.
- **Data fetching**: Prefer `useAsyncData` with a unique key (e.g. `time-blocks-${uid}-${day}`), async fetcher, and `watch` for reactive params. Return `{ data, refresh, pending }`.

## Data and server

- **useAsyncData**: Use for client-side fetches that depend on reactive state. Key must be unique per logical query; include reactive identifiers in the key.
- **useFetch**: Use when the URL/params are the only input; for Supabase or custom clients, `useAsyncData` + client call is usually clearer.
- **Lazy**: Prefer non-lazy by default for above-the-fold data; use `lazy: true` when appropriate for secondary content.

## Auth and routing

- **Supabase user**: `useSupabaseUser()` in middleware or setup. Redirect with `navigateTo('/login')` when unauthenticated.
- **Middleware**: `app/middleware/*.ts` — export default `defineNuxtRouteMiddleware((to, from) => { ... })`. Name file for usage (e.g. `auth.ts` → `middleware: 'auth'`).

## Config (nuxt.config.ts)

- **defineNuxtConfig**: Set `compatibilityDate`, `srcDir`, `modules`, and module options (e.g. `supabase`, `pwa`, `app.head`).
- **Aliases**: `~` and `@` typically point to `srcDir`; use `~/` for app code.

## Conventions

- Use `<script setup lang="ts">` in Vue SFCs.
- Prefer composables for shared state and side effects; keep pages thin and delegate to components and composables.
- Use `ref`/`computed`/`watch` from Vue; no need to import when using auto-imports (Nuxt provides them).

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/yulei-chen) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
