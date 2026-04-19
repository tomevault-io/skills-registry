---
name: nextjs-patterns
description: Provide code generation patterns and templates for Next.js (App Router, Server Components, Route Handlers, Server Actions). Use when the user mentions Next.js, App Router, Route Handlers, Server Components, or asks for Next.js templates, patterns, or scaffolding. Use when this capability is needed.
metadata:
  author: melnicenkovadik
---

# Next.js Patterns

## Quick start

1. Default to the App Router with TypeScript.
2. Prefer Server Components; add `"use client"` only when needed.
3. Put pages in `app/` and APIs in `app/api/.../route.ts`.
4. Use `fetch` on the server with cache controls; avoid client fetch when server can do it.
5. Return file paths plus minimal templates.

## Templates

### Server page

```tsx
import type { Metadata } from "next";

export const metadata: Metadata = {
  title: "Page title",
  description: "Page description",
};

export default async function Page() {
  return <main>Content</main>;
}
```

### Root layout

```tsx
export default function RootLayout({
  children,
}: {
  children: React.ReactNode;
}) {
  return (
    <html lang="en">
      <body>{children}</body>
    </html>
  );
}
```

### Client component

```tsx
"use client";

import { useState } from "react";

export function Counter() {
  const [count, setCount] = useState(0);
  return <button onClick={() => setCount(count + 1)}>{count}</button>;
}
```

### Route handler

```ts
import { NextResponse } from "next/server";

export async function GET() {
  return NextResponse.json({ ok: true });
}

export async function POST(request: Request) {
  const body = await request.json();
  return NextResponse.json({ ok: true, body });
}
```

### Server action

```ts
"use server";

export async function createItem(formData: FormData) {
  const name = String(formData.get("name") ?? "");
  // Validate and persist
  return { ok: true };
}
```

### Loading UI

```tsx
export default function Loading() {
  return <p>Loading...</p>;
}
```

### Error boundary

```tsx
"use client";

export default function Error({
  error,
  reset,
}: {
  error: Error;
  reset: () => void;
}) {
  return (
    <div>
      <p>Something went wrong.</p>
      <button onClick={() => reset()}>Retry</button>
    </div>
  );
}
```

### Data fetching with caching

```ts
const res = await fetch("https://api.example.com/items", {
  next: { revalidate: 60 },
});
const data = await res.json();
```

## Notes

- Use `generateMetadata` for dynamic metadata.
- Use `revalidateTag` and `revalidatePath` for on-demand revalidation.
- Keep route handlers side-effect free and return `NextResponse`.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/melnicenkovadik) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
