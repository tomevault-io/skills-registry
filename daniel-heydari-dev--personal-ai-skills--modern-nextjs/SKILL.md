---
name: modern-next-js
description: Next.js App Router best practices Use when this capability is needed.
metadata:
  author: daniel-heydari-dev
---

# Skill: Modern Next.js

Build performant Next.js applications with the App Router.

## App Router Structure

### Rules

- ✅ DO: Use the App Router (`app/` directory)
- ✅ DO: Organize by feature/route
- ✅ DO: Use file conventions (`page.tsx`, `layout.tsx`, `loading.tsx`)
- ❌ DON'T: Mix pages and app router

### File Conventions

| File            | Purpose                        |
| --------------- | ------------------------------ |
| `page.tsx`      | Route UI                       |
| `layout.tsx`    | Shared layout (wraps children) |
| `loading.tsx`   | Loading UI (Suspense)          |
| `error.tsx`     | Error boundary                 |
| `not-found.tsx` | 404 UI                         |
| `route.tsx`     | API endpoint                   |

### Example Structure

```
app/
├── layout.tsx           # Root layout
├── page.tsx             # Home page (/)
├── loading.tsx          # Global loading
├── error.tsx            # Global error
├── (auth)/              # Route group (no URL segment)
│   ├── login/
│   │   └── page.tsx     # /login
│   └── register/
│       └── page.tsx     # /register
├── dashboard/
│   ├── layout.tsx       # Dashboard layout
│   ├── page.tsx         # /dashboard
│   └── settings/
│       └── page.tsx     # /dashboard/settings
└── api/
    └── users/
        └── route.tsx    # /api/users
```

## Server vs Client Components

### Rules

- ✅ DO: Default to Server Components
- ✅ DO: Add `'use client'` only when needed
- ✅ DO: Keep Client Components at the leaves
- ❌ DON'T: Use `'use client'` on everything
- ❌ DON'T: Import Server Components into Client Components

### When to Use Client Components

| Need                      | Component Type |
| ------------------------- | -------------- |
| useState, useEffect       | Client         |
| Event listeners (onClick) | Client         |
| Browser APIs              | Client         |
| Data fetching (await)     | Server         |
| Database access           | Server         |
| Sensitive data/secrets    | Server         |

### Examples

```typescript
// Server Component (default) - no directive needed
async function UserList() {
  const users = await db.users.findMany(); // Direct DB access!

  return (
    <ul>
      {users.map(user => (
        <li key={user.id}>{user.name}</li>
      ))}
    </ul>
  );
}

// Client Component - needs interactivity
'use client';

import { useState } from 'react';

function Counter() {
  const [count, setCount] = useState(0);

  return (
    <button onClick={() => setCount(c => c + 1)}>
      Count: {count}
    </button>
  );
}

// ✅ Good - Client Component at leaf
// ServerParent.tsx (Server Component)
async function ServerParent() {
  const data = await fetchData();

  return (
    <div>
      <h1>{data.title}</h1>
      <InteractiveChild items={data.items} />
    </div>
  );
}

// InteractiveChild.tsx
'use client';
function InteractiveChild({ items }: { items: Item[] }) {
  const [selected, setSelected] = useState<string | null>(null);
  // ...
}
```

## Data Fetching

### Rules

- ✅ DO: Fetch data in Server Components
- ✅ DO: Use async/await directly
- ✅ DO: Colocate data fetching with components that need it
- ✅ DO: Use `fetch` with caching options
- ❌ DON'T: Use useEffect for initial data in Server Components

### Examples

```typescript
// ✅ Good - fetch in Server Component
async function ProductPage({ params }: { params: { id: string } }) {
  const product = await fetch(`/api/products/${params.id}`, {
    next: { revalidate: 3600 }, // Cache for 1 hour
  }).then(res => res.json());

  return <ProductDetails product={product} />;
}

// ✅ Good - parallel data fetching
async function Dashboard() {
  // Fetch in parallel
  const [user, stats, notifications] = await Promise.all([
    fetchUser(),
    fetchStats(),
    fetchNotifications(),
  ]);

  return (
    <>
      <UserHeader user={user} />
      <StatsCards stats={stats} />
      <NotificationList notifications={notifications} />
    </>
  );
}

// ✅ Good - streaming with Suspense
async function Page() {
  return (
    <div>
      <h1>Dashboard</h1>
      <Suspense fallback={<StatsSkeleton />}>
        <Stats /> {/* Slow component streams in */}
      </Suspense>
      <Suspense fallback={<ChartSkeleton />}>
        <Chart /> {/* Another slow component */}
      </Suspense>
    </div>
  );
}
```

## Server Actions

### Rules

- ✅ DO: Use Server Actions for mutations
- ✅ DO: Add `'use server'` directive
- ✅ DO: Validate input on the server
- ✅ DO: Use `revalidatePath` / `revalidateTag` after mutations
- ❌ DON'T: Trust client input without validation

### Examples

```typescript
// app/actions.ts
'use server';

import { revalidatePath } from 'next/cache';
import { redirect } from 'next/navigation';
import { z } from 'zod';

const CreatePostSchema = z.object({
  title: z.string().min(1).max(100),
  content: z.string().min(1),
});

export async function createPost(formData: FormData) {
  // Validate input
  const result = CreatePostSchema.safeParse({
    title: formData.get('title'),
    content: formData.get('content'),
  });

  if (!result.success) {
    return { error: result.error.flatten() };
  }

  // Get authenticated user
  const session = await getSession();
  if (!session) {
    redirect('/login');
  }

  // Create post
  await db.posts.create({
    data: {
      ...result.data,
      authorId: session.user.id,
    },
  });

  // Revalidate and redirect
  revalidatePath('/posts');
  redirect('/posts');
}

// Usage in component
function NewPostForm() {
  return (
    <form action={createPost}>
      <input name="title" required />
      <textarea name="content" required />
      <button type="submit">Create Post</button>
    </form>
  );
}
```

## Caching

### Caching Layers

| Layer               | Default               | Override                                 |
| ------------------- | --------------------- | ---------------------------------------- |
| Request Memoization | Automatic for `fetch` | N/A                                      |
| Data Cache          | Cached                | `{ cache: 'no-store' }`                  |
| Full Route Cache    | Static routes cached  | `export const dynamic = 'force-dynamic'` |

### Examples

```typescript
// Cached (default)
const data = await fetch(url);

// No cache
const data = await fetch(url, { cache: "no-store" });

// Revalidate after 1 hour
const data = await fetch(url, { next: { revalidate: 3600 } });

// Cache tags for on-demand revalidation
const data = await fetch(url, { next: { tags: ["posts"] } });

// Revalidate by tag
import { revalidateTag } from "next/cache";
revalidateTag("posts");

// Force dynamic rendering
export const dynamic = "force-dynamic";
```

## Route Handlers (API Routes)

### Rules

- ✅ DO: Use for webhooks, external API integration
- ✅ DO: Return proper status codes
- ✅ DO: Validate request bodies
- ❌ DON'T: Use for data accessed by your own app (use Server Components)

### Examples

```typescript
// app/api/users/route.ts
import { NextRequest, NextResponse } from "next/server";

export async function GET(request: NextRequest) {
  const searchParams = request.nextUrl.searchParams;
  const page = parseInt(searchParams.get("page") || "1");

  const users = await db.users.findMany({
    skip: (page - 1) * 20,
    take: 20,
  });

  return NextResponse.json(users);
}

export async function POST(request: NextRequest) {
  const body = await request.json();

  // Validate
  const result = UserSchema.safeParse(body);
  if (!result.success) {
    return NextResponse.json(
      { error: result.error.flatten() },
      { status: 400 },
    );
  }

  const user = await db.users.create({ data: result.data });

  return NextResponse.json(user, { status: 201 });
}

// Dynamic route: app/api/users/[id]/route.ts
export async function GET(
  request: NextRequest,
  { params }: { params: { id: string } },
) {
  const user = await db.users.findUnique({ where: { id: params.id } });

  if (!user) {
    return NextResponse.json({ error: "User not found" }, { status: 404 });
  }

  return NextResponse.json(user);
}
```

## Metadata

### Rules

- ✅ DO: Use `generateMetadata` for dynamic metadata
- ✅ DO: Include Open Graph and Twitter metadata
- ✅ DO: Set appropriate `robots` directives

### Examples

```typescript
// Static metadata
export const metadata: Metadata = {
  title: "My App",
  description: "Description of my app",
  openGraph: {
    title: "My App",
    description: "Description",
    images: ["/og-image.png"],
  },
};

// Dynamic metadata
export async function generateMetadata({
  params,
}: {
  params: { id: string };
}): Promise<Metadata> {
  const product = await fetchProduct(params.id);

  return {
    title: product.name,
    description: product.description,
    openGraph: {
      images: [product.image],
    },
  };
}
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/daniel-heydari-dev) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
