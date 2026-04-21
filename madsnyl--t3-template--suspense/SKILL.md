---
name: suspense-and-loading
description: Use loading.tsx files and React Suspense to split data fetching across multiple async components with skeleton loaders. Each page.tsx gets a matching loading.tsx, and async data components are wrapped in Suspense boundaries with skeleton fallbacks that mimic component design using the Skeleton UI. Use when this capability is needed.
metadata:
  author: madsnyl
---

# Suspense & Loading Skill: Split Data Fetching with Async Components

You implement **loading.tsx files** alongside every **page.tsx** to provide instant UI feedback, and split data fetching into multiple **async components** wrapped in **Suspense boundaries** with **skeleton fallbacks** that mirror the final component design.

This skill enables **progressive enhancement** and **granular loading states**—instead of blocking the entire page on one slow query, you fetch data in parallel across multiple components, each showing its own loader while data streams in.

---

## When to use this skill

Use this skill when the user asks to:
- add loading states to pages
- improve page load perception and responsiveness
- split data fetching across multiple components
- create skeleton loaders that match component layouts
- implement Suspense boundaries around async data components
- improve Core Web Vitals (especially LCP and FID)

---

## Core principles

1. **Every page gets a loading.tsx**: Provides instant feedback while the layout renders.
2. **Granular Suspense boundaries**: Wrap each async data component, not the entire page.
3. **Async components fetch data**: Server Components are async by default; fetch in the component body.
4. **Skeleton loaders mirror design**: Use the Skeleton UI component to match final component dimensions and layout.
5. **Loaders in component files**: Keep loading functions alongside components in the same file; make them async and reusable.
6. **Parallel data fetching**: Use `Promise.all()` to fetch multiple data sources in parallel within a component.
7. **Deterministic rendering**: Server Components render deterministically; Suspense handles async resolution.

---

## File structure conventions

```
src/app/
  ├─ page.tsx           (async Server Component)
  ├─ loading.tsx        (instant skeleton layout for entire page)
  ├─ admin/
  │  ├─ page.tsx        (async Server Component)
  │  ├─ loading.tsx     (skeleton for admin page)
  │  └─ _components/
  │     ├─ user-list.tsx       (async component with loading function inside)
  │     ├─ dashboard-stats.tsx  (async component with loading function inside)
  │     └─ user-form.tsx        (client component, no loading needed)
  └─ users/
     ├─ page.tsx
     ├─ loading.tsx
     └─ _components/
        └─ user-detail.tsx
```

**Key conventions:**
- `loading.tsx` matches folder structure exactly with same route.
- Private components in `_components/` keep loading skeletons inline.
- File-local loading functions are **async**, reusable, and match component shape.

---

## Hard rules

1. **loading.tsx structure**
   - Must be a default export of a React component (no "use client").
   - Returns skeleton UI that matches the page layout exactly.
   - Lives in the same directory as the `page.tsx` it serves.
   - Never imports data or server actions (skeleton only).

2. **Async components**
   - Can be Server Components (`async` function) or inside `page.tsx`.
   - Fetch data in the component body (top-level, not in renders/effects).
   - Wrap the component in `<Suspense>` when used in `page.tsx`.
   - Always have a `fallback` prop pointing to a skeleton loader.

3. **Loading functions**
   - **Location**: defined in the same file as the component (near the component or above it).
   - **Async**: marked `async` to handle Promise resolution.
   - **Reusable**: exported if used in multiple places; otherwise private.
   - **Naming**: `loadXxx()` or `fetchXxx()` (verb-based, clear intent).
   - **Returns**: the exact data type the component expects.
   - **No side effects**: only query/compute; never mutate state outside the function.

4. **Skeleton loaders**
   - Use shadcn/ui `Skeleton` component.
   - Mirror the component's layout exactly (same grid, spacing, element count).
   - Return JSX that fills the space; users should not see layout shift.
   - Animate subtly (Skeleton has a built-in shimmer effect).

5. **Suspense boundaries**
   - Wrap async components in `<Suspense fallback={<SkeletonXxx />}>`.
   - Each boundary should cover ONE logical data fetch unit.
   - Never nest deeply; keep boundaries at the page level or top of component tree.
   - Do not use Suspense for client-side state or effects (only server data).

6. **Error handling**
   - Async components that throw errors are caught by **Error Boundaries** (implement a separate `error.tsx`).
   - Do not catch errors inside async components; let them propagate.
   - For individual fallible operations, wrap in try-catch in the loading function.

---

## Canonical pattern: Async component + loading function + Suspense boundary

### Step 1: Define the loading function in the component file

```ts
// src/app/admin/_components/user-list.tsx
"use server";

import { Skeleton } from "~/components/ui/skeleton";
import { getUsers } from "~/services";


// Skeleton loader that mirrors UserList layout
export async function UserListSkeleton() {
  return (
    <div className="space-y-4">
      {Array.from({ length: 5 }).map((_, i) => (
        <div key={i} className="flex gap-4 rounded border p-4">
          <Skeleton className="h-12 w-12 rounded-full" />
          <div className="flex-1 space-y-2">
            <Skeleton className="h-4 w-48" />
            <Skeleton className="h-4 w-full" />
          </div>
        </div>
      ))}
    </div>
  );
}

// Async Server Component
export default async function UserList({ page = 1, pageSize = 10 }) {
  const { items, totalPages } = await getUsers(page, pageSize);

  return (
    <div className="space-y-4">
      {items.map((user) => (
        <div key={user.id} className="flex gap-4 rounded border p-4">
          <Avatar src={user.avatar} alt={user.name} />
          <div>
            <p className="font-semibold">{user.name}</p>
            <p className="text-sm text-muted-foreground">{user.email}</p>
          </div>
        </div>
      ))}
      <Pagination total={totalPages} current={page} />
    </div>
  );
}
```

### Step 2: Wrap the async component in Suspense on the page

```ts
// src/app/admin/page.tsx
import { Suspense } from "react";
import { UserList, UserListSkeleton } from "./_components/user-list";
import { DashboardStats, DashboardStatsSkeleton } from "./_components/dashboard-stats";

export default async function AdminPage() {
  return (
    <div className="space-y-8">
      <h1 className="text-3xl font-bold">Admin Dashboard</h1>

      {/* Suspense boundary for stats */}
      <Suspense fallback={<DashboardStatsSkeleton />}>
        <DashboardStats />
      </Suspense>

      {/* Suspense boundary for user list */}
      <Suspense fallback={<UserListSkeleton />}>
        <UserList page={1} pageSize={10} />
      </Suspense>
    </div>
  );
}
```

### Step 3: Create a loading.tsx for the route

```ts
// src/app/admin/loading.tsx
import { Skeleton } from "~/components/ui/skeleton";

export default async function AdminLoading() {
  return (
    <div className="space-y-8 p-6">
      {/* Keep static text in loading animation */}
      <h1 className="text-3xl font-bold">Admin Dashboard</h1>
      
      {/* Stats skeleton */}
      <div className="grid grid-cols-4 gap-4">
        {Array.from({ length: 4 }).map((_, i) => (
          <div key={i} className="rounded border p-4">
            <Skeleton className="mb-2 h-4 w-24" />
            <Skeleton className="h-8 w-16" />
          </div>
        ))}
      </div>

      {/* User list skeleton */}
      <div className="space-y-4">
        {Array.from({ length: 5 }).map((_, i) => (
          <div key={i} className="flex gap-4 rounded border p-4">
            <Skeleton className="h-12 w-12 rounded-full" />
            <div className="flex-1 space-y-2">
              <Skeleton className="h-4 w-48" />
              <Skeleton className="h-4 w-full" />
            </div>
          </div>
        ))}
      </div>
    </div>
  );
}
```

---

## Multi-component async pattern: Parallel data fetching

When a component needs multiple data sources, fetch them in parallel:

```ts
// src/app/dashboard/_components/dashboard-stats.tsx
import { Skeleton } from "~/components/ui/skeleton";
import { getStats, getRecentActivity, getCharts } from "~/services";

async function loadDashboard() {
  // Fetch all data in parallel
  const [stats, activity, charts] = await Promise.all([
    getStats(),
    getRecentActivity(),
    getCharts(),
  ]);

  return { stats, activity, charts };
}

export async function DashboardStatsSkeleton() {
  return (
    <div className="space-y-6">
      {/* Stats grid skeleton */}
      <div className="grid grid-cols-3 gap-4">
        {Array.from({ length: 3 }).map((_, i) => (
          <div key={i} className="rounded-lg border p-6">
            <Skeleton className="mb-2 h-4 w-24" />
            <Skeleton className="h-8 w-20" />
          </div>
        ))}
      </div>

      {/* Activity skeleton */}
      <div className="rounded-lg border p-6">
        <Skeleton className="mb-4 h-6 w-32" />
        <div className="space-y-3">
          {Array.from({ length: 4 }).map((_, i) => (
            <Skeleton key={i} className="h-12 w-full" />
          ))}
        </div>
      </div>

      {/* Chart skeleton */}
      <div className="rounded-lg border p-6">
        <Skeleton className="mb-4 h-6 w-32" />
        <Skeleton className="h-64 w-full" />
      </div>
    </div>
  );
}

export async function DashboardStats() {
  const { stats, activity, charts } = await loadDashboard();

  return (
    <div className="space-y-6">
      {/* Stats section */}
      <div className="grid grid-cols-3 gap-4">
        {stats.map((stat) => (
          <Card key={stat.id}>
            <CardHeader>
              <p className="text-sm text-muted-foreground">{stat.label}</p>
            </CardHeader>
            <CardContent>
              <p className="text-2xl font-bold">{stat.value}</p>
              <p className="text-xs text-green-600">+{stat.change}%</p>
            </CardContent>
          </Card>
        ))}
      </div>

      {/* Activity section */}
      <Card>
        <CardHeader>
          <CardTitle>Recent Activity</CardTitle>
        </CardHeader>
        <CardContent>
          {activity.map((item) => (
            <ActivityItem key={item.id} item={item} />
          ))}
        </CardContent>
      </Card>

      {/* Charts section */}
      <Card>
        <CardHeader>
          <CardTitle>Analytics</CardTitle>
        </CardHeader>
        <CardContent>
          <AnalyticsChart data={charts} />
        </CardContent>
      </Card>
    </div>
  );
}
```

---

## Conditional async components and Suspense

For components that only render conditionally, wrap in Suspense at the point of use:

```ts
// src/app/users/page.tsx
import { Suspense } from "react";
import { UserDetail, UserDetailSkeleton } from "./_components/user-detail";

export default async function UsersPage({ searchParams }) {
  const { id } = searchParams;

  return (
    <div>
      <h1>Users</h1>

      {/* Only render detail if ID is provided */}
      {id && (
        <Suspense fallback={<UserDetailSkeleton />}>
          <UserDetail userId={id} />
        </Suspense>
      )}
    </div>
  );
}
```

---

## Error boundaries alongside loading states

Create an `error.tsx` in the same directory as `loading.tsx` to handle async errors:

```ts
// src/app/admin/error.tsx
"use client";

import { useEffect } from "react";
import { AlertCircle } from "lucide-react";
import { Button } from "~/components/ui/button";

export default function AdminError({
  error,
  reset,
}: {
  error: Error & { digest?: string };
  reset: () => void;
}) {
  useEffect(() => {
    console.error("Admin page error:", error);
  }, [error]);

  return (
    <div className="flex min-h-screen items-center justify-center">
      <div className="max-w-md space-y-4 text-center">
        <AlertCircle className="mx-auto h-12 w-12 text-red-500" />
        <h2 className="text-xl font-semibold">Something went wrong</h2>
        <p className="text-sm text-muted-foreground">{error.message}</p>
        <Button onClick={reset}>Try again</Button>
      </div>
    </div>
  );
}
```

---

## Best practices

1. **Skeleton granularity**: One skeleton per Suspense boundary. Don't create one huge skeleton for the entire page if you have multiple Suspense regions.

2. **Avoid overfetching in loading functions**: The loading function should be as efficient as the regular component—same database queries, same selections.

3. **Keep loading.tsx minimal**: It should be static HTML with Skeleton components. No dynamic content, no server functions.

4. **Suspense placement matters**: Place boundaries around the slowest async operation, not around the entire page. This allows faster sections to render immediately.

5. **Naming clarity**: Use `load*` or `fetch*` prefixes for async functions. Use `*Skeleton` for fallback components. Makes code scannable.

6. **Reuse loading functions**: If multiple Suspense boundaries render the same component, they can share the same loading function.

7. **Test progressive enhancement**: Turn on network throttling in DevTools to verify that loading states appear before data arrives.

---

## Common mistakes to avoid

❌ **Mistake 1**: Putting the entire page in one Suspense boundary
```ts
// BAD
export default async function Page() {
  return (
    <Suspense fallback={<PageSkeleton />}>
      <Header /> {/* Blocks on slow query */}
      <Sidebar /> {/* Blocks on slow query */}
      <SlowDataComponent />
    </Suspense>
  );
}
```

✅ **Better**: Granular boundaries
```ts
// GOOD
export default async function Page() {
  return (
    <>
      <Header />
      <Sidebar />
      <Suspense fallback={<SlowDataSkeleton />}>
        <SlowDataComponent />
      </Suspense>
    </>
  );
}
```

❌ **Mistake 2**: Loading logic in loading.tsx
```ts
// BAD - loading.tsx is calling getUsers!
export default async function AdminLoading() {
  const users = await getUsers(); // 🚨 This is async, defeats purpose
  return <div>{users.length} users loading...</div>;
}
```

✅ **Correct**: loading.tsx is static
```ts
// GOOD - Just a skeleton, no logic
export default async function AdminLoading() {
  return (
    <div>
      <Skeleton className="h-10 w-64 mb-4" />
      <div className="space-y-4">
        {Array.from({ length: 5 }).map((_, i) => (
          <Skeleton key={i} className="h-12" />
        ))}
      </div>
    </div>
  );
}
```

❌ **Mistake 3**: Skeleton that doesn't match component size
```ts
// BAD - Skeleton is too small, causes layout shift
export async function UserListSkeleton() {
  return <Skeleton className="h-4 w-20" />; // Way too small!
}
```

✅ **Correct**: Skeleton matches final dimensions
```ts
// GOOD - Matches UserList exactly
export async function UserListSkeleton() {
  return (
    <div className="space-y-4">
      {Array.from({ length: 5 }).map((_, i) => (
        <div key={i} className="flex gap-4 rounded border p-4">
          <Skeleton className="h-12 w-12 rounded-full" />
          <div className="flex-1 space-y-2">
            <Skeleton className="h-4 w-48" />
            <Skeleton className="h-4 w-full" />
          </div>
        </div>
      ))}
    </div>
  );
}
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/madsnyl) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
