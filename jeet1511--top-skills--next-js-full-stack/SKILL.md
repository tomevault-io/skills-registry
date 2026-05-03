---
name: next-js-full-stack
description: Build production-ready Next.js applications — App Router, Server Components, API routes, and deployment. Use when this capability is needed.
metadata:
  author: Jeet1511
---

# Next.js Full Stack

## App Router Structure

```
app/
  layout.tsx          # Root layout (wraps all pages)
  page.tsx            # Home page (/)
  globals.css         # Global styles
  dashboard/
    layout.tsx        # Dashboard layout
    page.tsx          # /dashboard
    settings/
      page.tsx        # /dashboard/settings
  api/
    users/
      route.ts        # API: /api/users
```

## Server Components (default)

Components in the App Router are Server Components by default. They run on the server and can directly access databases, env vars, and file systems.

```tsx
// This runs on the server — no "use client" needed
export default async function UsersPage() {
  const users = await db.query("SELECT * FROM users");
  return (
    <main>
      <h1>Users</h1>
      <ul>{users.map(u => <li key={u.id}>{u.name}</li>)}</ul>
    </main>
  );
}
```

## Client Components

Add `"use client"` only when you need browser APIs, state, or event handlers:

```tsx
"use client";
import { useState } from "react";

export function SearchBar({ onSearch }: { onSearch: (q: string) => void }) {
  const [query, setQuery] = useState("");
  return (
    <input
      value={query}
      onChange={(e) => setQuery(e.target.value)}
      onKeyDown={(e) => e.key === "Enter" && onSearch(query)}
      placeholder="Search..."
    />
  );
}
```

## API Routes

```typescript
// app/api/users/route.ts
import { NextResponse } from "next/server";

export async function GET(request: Request) {
  const users = await db.query("SELECT * FROM users");
  return NextResponse.json(users);
}

export async function POST(request: Request) {
  const body = await request.json();
  const user = await db.insert("users", body);
  return NextResponse.json(user, { status: 201 });
}
```

## Server Actions

```tsx
// actions.ts
"use server";

export async function createPost(formData: FormData) {
  const title = formData.get("title") as string;
  await db.insert("posts", { title });
  revalidatePath("/posts");
}
```

## Metadata and SEO

```tsx
export const metadata = {
  title: "My App",
  description: "Production-ready Next.js application",
  openGraph: { title: "My App", description: "..." },
};
```

## Middleware

```typescript
// middleware.ts (at project root)
import { NextResponse } from "next/server";
import type { NextRequest } from "next/server";

export function middleware(request: NextRequest) {
  const token = request.cookies.get("session");
  if (!token && request.nextUrl.pathname.startsWith("/dashboard")) {
    return NextResponse.redirect(new URL("/login", request.url));
  }
  return NextResponse.next();
}

export const config = { matcher: ["/dashboard/:path*"] };
```

## Key Rules

- Default to Server Components. Only add `"use client"` when necessary.
- Use `loading.tsx` for loading states and `error.tsx` for error boundaries.
- Use `generateStaticParams()` for static paths.
- Use `revalidatePath()` or `revalidateTag()` for cache invalidation.
- Put shared layouts in `layout.tsx`, not in individual pages.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/Jeet1511) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
