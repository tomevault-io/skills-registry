---
name: tanstack-comprehensive
description: Tanstack Start, Router, and Query patterns for routing, data fetching, server functions, and tRPC integration. Use when working with routes, loaders, queries, mutations, search params, navigation, or server-side operations. Use when this capability is needed.
metadata:
  author: retrip-ai
---

# Tanstack Comprehensive

Complete guide to Tanstack Start, Router, and Query patterns for full-stack type-safe applications.

## Overview

This skill covers the complete Tanstack ecosystem used in this project:
- **Tanstack Router** - File-based routing with type-safe navigation
- **Tanstack Query** - Data fetching, caching, and state management
- **Tanstack Start** - Server-side rendering and data loading
- **tRPC Integration** - Type-safe API calls

## When to Apply

Reference these guidelines when:
- Creating or modifying routes
- Implementing data fetching (client or server-side)
- Working with loaders, queries, or mutations
- Handling navigation or URL parameters
- Integrating forms with server operations
- Optimizing data loading patterns

## Quick Reference

### Critical Patterns

**File-Based Routing:**
- Pages: `dashboard/index.tsx` → `/dashboard`
- Layouts: `_authenticated.tsx` → Wraps child routes
- Dynamic: `users/$id.tsx` → `/users/123`

**Data Loading (Recommended Pattern):**
```typescript
// loader + pendingComponent + useSuspenseQuery
export const Route = createFileRoute('/page')({
  loader: async ({ context }) => {
    await context.queryClient.ensureQueryData(
      context.trpc.myData.queryOptions()
    );
  },
  pendingComponent: MySkeleton,
  component: MyPage,
});

function MyPage() {
  const trpc = useTrpc();
  const { data } = useSuspenseQuery(trpc.myData.queryOptions());
  return <div>{data.name}</div>;
}
```

**tRPC Integration:**
```typescript
// ✅ CORRECT
const { data } = useQuery(trpc.organizations.getCurrent.queryOptions());

// ❌ WRONG - This method doesn't exist
const { data } = trpc.organizations.getCurrent.useQuery();
```

**Navigation:**
```typescript
<Link to="/users/$id" params={{ id: '123' }}>User</Link>
```

**Search Params (Avoid Re-renders):**
```typescript
// ✅ Read on demand
const router = useRouter();
const ref = router.latestLocation.search.ref;

// ❌ Subscribes to all changes
const search = useSearch({ from: '__root__' });
const ref = search.ref;
```

### Architecture

```
workspace (Tanstack Start)
  ↓ tRPC client
api-trpc (Hono + tRPC)
  ↓ Drizzle ORM
Database (PostgreSQL)
```

**Key Principle**: Never access database directly. Always use tRPC procedures.

## References

Complete documentation with examples:

- `references/routing.md` - File-based routing, navigation, params, loaders, authentication
- `references/data-fetching.md` - Queries, mutations, tRPC integration, SSR patterns, Mastra queries
- `references/server-functions.md` - Server-side operations, tRPC from workspace, error handling

To find specific patterns:
```bash
grep -l "loader" references/*.md
grep -l "useSuspenseQuery" references/*.md
grep -l "search params" references/*.md
```

## Core Concepts

### 1. Routing Patterns

**File Structure Determines URLs:**
- `index.tsx` → Route root (e.g., `/dashboard`)
- `_layout.tsx` → Layout wrapper (doesn't affect URL)
- `$param.tsx` → Dynamic segment
- Nested folders → Nested routes

**Type-Safe Navigation:**
```typescript
// Link component
<Link to="/users/$id" params={{ id }}>User</Link>

// Programmatic
const navigate = useNavigate();
navigate({ to: '/dashboard', search: { tab: 'overview' } });

// In component
const { id } = Route.useParams(); // Type-safe
const { tab } = Route.useSearch(); // Type-safe
```

### 2. Data Loading Patterns

**Recommended: loader + pendingComponent + useSuspenseQuery**

Why this pattern?
- Skeleton shows immediately (no blank screen)
- No duplicate queries (loader preloads, component reads cache)
- Simpler component code (data always available)
- Better UX (immediate visual feedback)

```typescript
export const Route = createFileRoute('/dashboard')({
  loader: async ({ context }) => {
    // Preload all data in parallel
    await Promise.all([
      context.queryClient.ensureQueryData(
        context.trpc.organizations.getCurrent.queryOptions()
      ),
      context.queryClient.ensureQueryData(
        context.trpc.members.list.queryOptions()
      ),
    ]);
  },
  pendingComponent: DashboardSkeleton,
  component: DashboardPage,
});

function DashboardPage() {
  const trpc = useTrpc();
  // Data guaranteed - no loading checks needed
  const { data: org } = useSuspenseQuery(
    trpc.organizations.getCurrent.queryOptions()
  );
  const { data: members } = useSuspenseQuery(
    trpc.members.list.queryOptions()
  );

  return <div>{org.name} - {members.length} members</div>;
}
```

**Anti-Pattern: useQuery + manual loading check**
```typescript
// ❌ DON'T DO THIS
function MyPage() {
  const { data, isLoading } = useQuery(...);

  if (isLoading || !data) {
    return <MySkeleton />; // Causes blank screen flash
  }

  return <div>{data.name}</div>;
}
```

### 3. tRPC Integration

**CRITICAL**: The project uses `createTRPCOptionsProxy` which provides factory functions, NOT hooks.

**Correct Usage:**
```typescript
import { useQuery, useMutation } from '@tanstack/react-query';
import { trpc } from '@/trpc';

// Queries
const { data } = useQuery(
  trpc.organizations.getCurrent.queryOptions()
);

// With params
const { data } = useQuery(
  trpc.users.getById.queryOptions({ id: '123' })
);

// Mutations
const mutation = useMutation(
  trpc.organizations.update.mutationOptions()
);

mutation.mutate({ name: 'New Name' });
```

**Query Invalidation:**
```typescript
import { useQueryClient } from '@tanstack/react-query';

const queryClient = useQueryClient();

// Invalidate specific query
queryClient.invalidateQueries({
  queryKey: [['organizations', 'getCurrent']],
});

// Invalidate all organization queries
queryClient.invalidateQueries({
  queryKey: [['organizations']],
});
```

### 4. Search Params Optimization

**Problem**: `useSearch()` subscribes to ALL search param changes, causing unnecessary re-renders.

**Solution**: Read search params on-demand using `router.latestLocation`.

```typescript
// ✅ CORRECT - No re-renders
import { useRouter } from '@tanstack/react-router';

function ShareButton({ chatId }: Props) {
  const router = useRouter();

  const handleShare = () => {
    const ref = router.latestLocation.search.ref;
    shareChat(chatId, { ref });
  };

  return <button onClick={handleShare}>Share</button>;
}

// ❌ WRONG - Re-renders on every search param change
import { useSearch } from '@tanstack/react-router';

function ShareButton({ chatId }: Props) {
  const search = useSearch({ from: '__root__' });

  const handleShare = () => {
    const ref = search.ref;
    shareChat(chatId, { ref });
  };

  return <button onClick={handleShare}>Share</button>;
}
```

### 5. Route Loaders

**Pre-load data before navigation:**

```typescript
export const Route = createFileRoute('/_authenticated/dashboard')({
  loader: async ({ context }) => {
    // Blocks navigation until data loaded
    await context.queryClient.ensureQueryData(
      context.trpc.organizations.getCurrent.queryOptions()
    );
  },
  pendingComponent: DashboardSkeleton,
  component: DashboardPage,
});
```

### 6. Protected Routes

```typescript
import { createFileRoute, redirect } from '@tanstack/react-router';

export const Route = createFileRoute('/_authenticated')({
  beforeLoad: async ({ context }) => {
    const session = await getSession(context);

    if (!session) {
      throw redirect({
        to: '/sign-in',
        search: { redirect: location.href },
      });
    }
  },
});
```

### 7. Mastra Queries

For AI chat data (messages, threads), use query functions instead of tRPC:

```typescript
// src/lib/mastra-queries.ts
export const threadMessagesQueryOptions = (threadId: string) => ({
  queryKey: ['mastra', 'messages', threadId] as const,
  queryFn: async () => {
    const client = createMastraClient();
    const { messages } = await client.listThreadMessages(threadId, {
      agentId: 'retripAgent',
    });
    return toAISdkV5Messages(messages);
  },
});

// In component
const { data: messages } = useSuspenseQuery(
  threadMessagesQueryOptions(threadId)
);
```

## Best Practices

### ✅ Do:

**Routing:**
- Use file-based routing for all pages
- Leverage type-safe params and search
- Use `Link` component for navigation
- Protect routes with `beforeLoad`
- Validate search params with Zod

**Data Fetching:**
- Use `ensureQueryData` in loaders with `pendingComponent`
- Use `useSuspenseQuery` for loader-prefetched data
- Use `queryOptions()` with TanStack Query hooks
- Invalidate queries after mutations
- Handle errors gracefully

**Performance:**
- Read search params on-demand (not reactively)
- Preload routes before navigation
- Load data in parallel with `Promise.all()`
- Use optimistic updates for instant feedback

### ❌ Don't:

**Routing:**
- Mix file-based and programmatic routing
- Use plain `<a>` tags for internal navigation
- Skip search param validation
- Create deeply nested layouts unnecessarily

**Data Fetching:**
- Use non-existent `.useQuery()` method on trpc object
- Use `useQuery` + manual `if (!data)` checks for loader data
- Forget `pendingComponent` when using loaders
- Skip error handling
- Query database directly from workspace
- Use `any` types

**Performance:**
- Subscribe to all search params when you only need one
- Skip route preloading on hover/focus
- Load data sequentially when it could be parallel

## Common Patterns

### Mutation with Invalidation

```typescript
const queryClient = useQueryClient();

const mutation = useMutation({
  ...trpc.organizations.update.mutationOptions(),
  onSuccess: () => {
    queryClient.invalidateQueries({
      queryKey: [['organizations', 'getCurrent']],
    });
    toast.success('Updated successfully');
  },
});
```

### Optimistic Updates

```typescript
const mutation = useMutation({
  ...trpc.apiKeys.delete.mutationOptions(),
  onMutate: async (deletedId) => {
    await queryClient.cancelQueries({
      queryKey: [['apiKeys', 'list']],
    });

    const previous = queryClient.getQueryData([['apiKeys', 'list']]);

    queryClient.setQueryData([['apiKeys', 'list']], (old: any) =>
      old?.filter((key: any) => key.id !== deletedId)
    );

    return { previous };
  },
  onError: (err, deletedId, context) => {
    queryClient.setQueryData([['apiKeys', 'list']], context?.previous);
  },
});
```

### Dependent Queries

```typescript
const { data: user } = useQuery(
  trpc.users.getById.queryOptions({ id: userId })
);

const { data: posts } = useQuery({
  ...trpc.posts.list.queryOptions({ authorId: user?.id }),
  enabled: !!user,
});
```

### Route Preloading

```typescript
import { Link, useRouter } from '@tanstack/react-router';

function Navigation() {
  const router = useRouter();

  return (
    <Link
      to="/dashboard"
      onMouseEnter={() => router.preloadRoute('/dashboard')}
      onFocus={() => router.preloadRoute('/dashboard')}
    >
      Dashboard
    </Link>
  );
}
```

## Related Skills

- `react-best-practices` - Performance optimization patterns
- `form-patterns` - Form handling with React Hook Form

---

**Version:** 1.0.0
**Last updated:** 2026-01-14

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/retrip-ai) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
