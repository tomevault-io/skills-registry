---
name: nextjs-shadcn
description: Next.js 15 App Router + shadcn/ui + Tailwind v4 + next-themes patterns for FlashBanana. Use when creating pages, layouts, components, API routes, or configuring theme/styling. Use when this capability is needed.
metadata:
  author: adminbjkai
---

# Next.js 15 + shadcn/ui + Tailwind v4 Patterns

## App Router File Conventions
- `page.tsx` — Route page (Server Component by default)
- `layout.tsx` — Persistent layout wrapper
- `loading.tsx` — Suspense fallback
- `error.tsx` — Error boundary (`"use client"` required)
- `route.ts` — API route handler (in `app/api/`)

## Server vs Client Components

```tsx
// SERVER (default) — no directive needed
// Can: fetch data, access env vars, read fs, use async/await
// Cannot: useState, useEffect, onClick, browser APIs
export default async function Page() {
  const data = await fetchData(); // Direct server fetch
  return <div>{data}</div>;
}

// CLIENT — must add directive
"use client"
// Can: useState, useEffect, onClick, useRef, browser APIs
// Cannot: async component, direct env var access (only NEXT_PUBLIC_)
import { useState } from "react";
export function Counter() {
  const [count, setCount] = useState(0);
  return <button onClick={() => setCount(c => c + 1)}>{count}</button>;
}
```

**Rule of thumb:** Start as Server Component. Add `"use client"` only when you get a build error about hooks/events.

## shadcn/ui Usage

### Adding Components
```bash
npx shadcn@latest add select dialog tabs button
```
Components land in `src/components/ui/`. Never modify these directly.

### Third-Party Registries (configured in components.json)
```bash
# Magic UI for polish effects
npx shadcn@latest add @magic-ui/shimmer-button

# Motion Primitives for animations
npx shadcn@latest add @motion-primitives/fade-in
```

### Composing Components
```tsx
// CORRECT: Import from ui/, compose in your component
import { Select, SelectContent, SelectItem, SelectTrigger, SelectValue } from "@/components/ui/select";
import { Label } from "@/components/ui/label";

export function ModelSelector({ value, onChange, models }) {
  return (
    <div className="space-y-2">
      <Label htmlFor="model">Model</Label>
      <Select value={value} onValueChange={onChange}>
        <SelectTrigger id="model">
          <SelectValue placeholder="Select model" />
        </SelectTrigger>
        <SelectContent>
          {models.map(m => (
            <SelectItem key={m.id} value={m.id}>{m.name}</SelectItem>
          ))}
        </SelectContent>
      </Select>
    </div>
  );
}
```

## Tailwind v4 Dark Mode

In `globals.css`:
```css
@import "tailwindcss";
@custom-variant dark (&:where(.dark, .dark *));
```

In components:
```tsx
<div className="bg-white dark:bg-zinc-950 text-zinc-900 dark:text-zinc-50">
```

## next-themes Setup

```tsx
// src/components/providers/theme-provider.tsx
"use client"
import { ThemeProvider as NextThemesProvider } from "next-themes"

export function ThemeProvider({ children, ...props }: React.ComponentProps<typeof NextThemesProvider>) {
  return <NextThemesProvider {...props}>{children}</NextThemesProvider>
}

// src/app/layout.tsx
import { ThemeProvider } from "@/components/providers/theme-provider"

export default function RootLayout({ children }) {
  return (
    <html lang="en" suppressHydrationWarning>
      <body>
        <ThemeProvider attribute="class" defaultTheme="dark" enableSystem disableTransitionOnChange>
          {children}
        </ThemeProvider>
      </body>
    </html>
  )
}
```

## API Routes

```tsx
// src/app/api/generate/route.ts
import { NextRequest, NextResponse } from "next/server";
import ai from "@/lib/gemini";

export async function POST(request: NextRequest) {
  try {
    const body = await request.json();
    // validate input...
    const response = await ai.models.generateContent({ ... });
    return NextResponse.json({ data: response });
  } catch (error) {
    return NextResponse.json(
      { error: error instanceof Error ? error.message : "Generation failed" },
      { status: 500 }
    );
  }
}
```

## Common Gotchas
1. **Hydration mismatch:** Theme-dependent rendering causes mismatch. Use `suppressHydrationWarning` on `<html>` and mount theme-dependent UI in client components with `useEffect`.
2. **Image component:** Use `next/image` with `unoptimized` for base64 generated images.
3. **Font loading:** Use `next/font/google` in layout.tsx, never `<link>` tags.
4. **Import alias:** Always use `@/` prefix (maps to `src/`).

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/adminbjkai) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
