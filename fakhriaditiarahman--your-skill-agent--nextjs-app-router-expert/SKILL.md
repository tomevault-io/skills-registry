---
name: nextjs-app-router-expert
description: > Use when this capability is needed.
metadata:
  author: fakhriaditiarahman
---

# @nextjs-app-router-expert

## 🎯 Role & Objectives

- **Embrace the Server**: Maximize use of React Server Components (RSC) for performance and SEO.
- **Data Fetching Strategy**: Implement granular caching and revalidation strategies.
- **UX Optimization**: Utilize Streaming and Suspense for instant loading states.
- **Type Safety**: Ensure end-to-end type safety from database (Prisma/Drizzle) to frontend.
- **Route Architecture**: Design scalable route groups and parallel routes.

---

## 🧠 Knowledge Base

### Core Concepts
- **React Server Components (RSC)**: Components that render on the server, reducing client bundle size.
- **Server Actions**: Functions that run on the server, callable directly from UI components (`'use server'`).
- **Middleware**: Code that executes before a request completes (auth, rewrite, redirect).
- **Route Handlers**: API endpoints defined in `route.ts` files (replacing `pages/api`).

### Data Fetching & Caching
- **`fetch` API Extension**: Next.js extends native fetch with caching options (`force-cache`, `no-store`).
- **`revalidatePath` / `revalidateTag`**: On-demand cache invalidation mechanisms.
- **`generateStaticParams`**: Determining static paths at build time for dynamic routes.

### Rendering Strategies
- **Static Site Generation (SSG)**: Default for non-dynamic data.
- **Server-Side Rendering (SSR)**: Dynamic data fetched at request time.
- **Incremental Static Regeneration (ISR)**: Updating static pages after deployment.
- **Partial Prerendering (PPR)**: (Experimental) Mixing static shell with dynamic holes.

---

## ⚙️ Operating Principles

- **Fetch on Server, Interact on Client**: Move as much logic as possible to RSCs.
- **Colocation**: Keep styles, tests, and components close to their routes.
- **Progressive Enhancement**: Ensure forms work without JavaScript using Server Actions.
- **Composition over Inheritance**: Build complex UIs from small, single-purpose components.

---

## 🏗️ Architecture Patterns

### 1. The "Hybrid" Component Tree
```mermaid
graph TD
    Page[Page (Server)] --> Layout[Layout (Server)]
    Layout --> Header[Header (Client)]
    Layout --> Main[Main Content (Server)]
    Main --> InteractiveFeed[Feed (Client)]
    InteractiveFeed --> FeedItem[Item (Server - Passed as Prop)]
```

### 2. Server Action Mutation Flow
```mermaid
graph LR
    Form[Client Form] -->|Submit| Action[Server Action]
    Action -->|Mutate| DB[Database]
    Action -->|Response| UI[Invalidate & Redirect]
```

---

## 💡 Best Practices

- **Use `layout.tsx` wisely**: Nested layouts prevent unnecessary re-rendering.
- **Image Optimization**: Always use `next/image` with proper sizing attributes.
- **Font Optimization**: Use `next/font` to eliminate layout shift (CLS).
- **Lazy Loading**: Use `next/dynamic` to load heavy client components on demand.
- ** SEO**: Use the Metadata API in `layout.tsx` or `page.tsx` instead of `<Head>`.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/fakhriaditiarahman) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
