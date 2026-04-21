---
name: nextjs-patterns
description: Next.js 15 best practices including App Router, Server Components, caching, and performance patterns. Use when building Next.js applications with modern patterns. Use when this capability is needed.
metadata:
  author: deomiarn
---

# Next.js Patterns

This skill covers modern Next.js 15 best practices for building high-performance websites.

## When to Use This Skill

- Setting up Next.js projects
- Implementing Server Components
- Configuring caching strategies
- Optimizing performance
- Handling data fetching

## Core Patterns

### 1. Project Structure

```
app/
├── (marketing)/          # Route group (no URL impact)
│   ├── page.tsx          # Home page
│   ├── about/
│   │   └── page.tsx
│   └── services/
│       └── page.tsx
├── (blog)/
│   └── blog/
│       ├── page.tsx      # Blog listing
│       └── [slug]/
│           └── page.tsx  # Blog post
├── api/
│   └── route.ts          # API routes
├── layout.tsx            # Root layout
├── not-found.tsx         # 404 page
├── error.tsx             # Error boundary
├── loading.tsx           # Loading UI
└── globals.css

components/
├── ui/                   # shadcn/ui components
│   ├── button.tsx
│   └── card.tsx
├── sections/             # Page sections
│   ├── hero.tsx
│   └── features.tsx
└── layout/               # Layout components
    ├── header.tsx
    └── footer.tsx

lib/
├── utils.ts              # Utility functions
├── constants.ts          # Constants
└── api.ts                # API helpers
```

### 2. Server Components (Default)

By default, all components in App Router are Server Components.

```tsx
// app/page.tsx - Server Component (default)
export default async function HomePage() {
  // Can fetch data directly
  const data = await fetch("https://api.example.com/data")
  const posts = await data.json()

  return (
    <main>
      {posts.map((post) => (
        <article key={post.id}>
          <h2>{post.title}</h2>
        </article>
      ))}
    </main>
  )
}
```

**Benefits of Server Components:**
- Zero client-side JavaScript for the component
- Direct database/API access
- Smaller bundle size
- Better SEO (content rendered on server)

### 3. Client Components

Use `"use client"` only when needed:
- Event handlers (onClick, onChange)
- Browser APIs (localStorage, window)
- React hooks (useState, useEffect)
- Third-party libraries that use client features

```tsx
"use client"

import { useState } from "react"
import { Button } from "@/components/ui/button"

export function Counter() {
  const [count, setCount] = useState(0)

  return (
    <Button onClick={() => setCount(count + 1)}>
      Count: {count}
    </Button>
  )
}
```

**Composition Pattern:**
```tsx
// Server Component (page.tsx)
import { Counter } from "@/components/counter"

export default async function Page() {
  const data = await fetchData() // Server-side fetch

  return (
    <div>
      <h1>{data.title}</h1>      {/* Server rendered */}
      <Counter />                 {/* Client component island */}
    </div>
  )
}
```

### 4. Data Fetching

**Server Component Fetching:**
```tsx
// app/blog/page.tsx
async function getPosts() {
  const res = await fetch("https://api.example.com/posts", {
    next: { revalidate: 3600 } // Revalidate every hour
  })
  return res.json()
}

export default async function BlogPage() {
  const posts = await getPosts()
  return <PostList posts={posts} />
}
```

**Parallel Data Fetching:**
```tsx
export default async function Page() {
  // Fetch in parallel, not sequentially
  const [posts, categories, featured] = await Promise.all([
    getPosts(),
    getCategories(),
    getFeaturedPost()
  ])

  return (
    <>
      <Featured post={featured} />
      <PostList posts={posts} categories={categories} />
    </>
  )
}
```

**Dynamic vs Static:**
```tsx
// Force dynamic rendering
export const dynamic = "force-dynamic"

// Force static rendering
export const dynamic = "force-static"

// Revalidate every N seconds
export const revalidate = 3600
```

### 5. Loading States

**loading.tsx:**
```tsx
// app/blog/loading.tsx
import { Skeleton } from "@/components/ui/skeleton"

export default function Loading() {
  return (
    <div className="space-y-4">
      <Skeleton className="h-12 w-3/4" />
      <Skeleton className="h-4 w-full" />
      <Skeleton className="h-4 w-full" />
      <Skeleton className="h-4 w-2/3" />
    </div>
  )
}
```

**Suspense for Streaming:**
```tsx
import { Suspense } from "react"

export default function Page() {
  return (
    <main>
      <h1>Blog</h1>
      <Suspense fallback={<PostsSkeleton />}>
        <Posts /> {/* Streams in when ready */}
      </Suspense>
    </main>
  )
}
```

### 6. Error Handling

**error.tsx:**
```tsx
"use client"

export default function Error({
  error,
  reset,
}: {
  error: Error & { digest?: string }
  reset: () => void
}) {
  return (
    <div className="flex flex-col items-center justify-center min-h-[400px]">
      <h2>Something went wrong!</h2>
      <Button onClick={() => reset()}>Try again</Button>
    </div>
  )
}
```

**not-found.tsx:**
```tsx
import Link from "next/link"

export default function NotFound() {
  return (
    <div className="flex flex-col items-center justify-center min-h-screen">
      <h1 className="text-6xl font-bold">404</h1>
      <p className="text-xl text-muted-foreground mt-4">
        Page not found
      </p>
      <Link href="/" className="mt-8">
        <Button>Go home</Button>
      </Link>
    </div>
  )
}
```

### 7. Route Handlers (API Routes)

```tsx
// app/api/posts/route.ts
import { NextResponse } from "next/server"

export async function GET() {
  const posts = await getPosts()
  return NextResponse.json(posts)
}

export async function POST(request: Request) {
  const body = await request.json()
  const post = await createPost(body)
  return NextResponse.json(post, { status: 201 })
}
```

### 8. Server Actions

```tsx
// app/actions.ts
"use server"

import { revalidatePath } from "next/cache"

export async function createPost(formData: FormData) {
  const title = formData.get("title") as string
  const content = formData.get("content") as string

  await db.post.create({
    data: { title, content }
  })

  revalidatePath("/blog")
}

// Usage in component
export default function CreatePostForm() {
  return (
    <form action={createPost}>
      <input name="title" required />
      <textarea name="content" required />
      <Button type="submit">Create</Button>
    </form>
  )
}
```

### 9. Image Optimization

```tsx
import Image from "next/image"

// Local image (imported)
import heroImage from "@/public/hero.jpg"

export function Hero() {
  return (
    <Image
      src={heroImage}
      alt="Hero image"
      placeholder="blur" // Automatic blur placeholder
      priority // Preload for above-fold images
    />
  )
}

// Remote image
<Image
  src="https://example.com/image.jpg"
  alt="Remote image"
  width={800}
  height={600}
  sizes="(max-width: 768px) 100vw, 50vw"
/>

// Fill container
<div className="relative aspect-video">
  <Image
    src="/image.jpg"
    alt="Image"
    fill
    className="object-cover"
  />
</div>
```

**next.config.js:**
```js
module.exports = {
  images: {
    remotePatterns: [
      {
        protocol: "https",
        hostname: "images.example.com",
      },
    ],
    formats: ["image/avif", "image/webp"],
  },
}
```

### 10. Font Optimization

```tsx
// app/layout.tsx
import { Inter, Playfair_Display } from "next/font/google"

const inter = Inter({
  subsets: ["latin"],
  variable: "--font-inter",
  display: "swap",
})

const playfair = Playfair_Display({
  subsets: ["latin"],
  variable: "--font-playfair",
  display: "swap",
})

export default function RootLayout({ children }) {
  return (
    <html className={`${inter.variable} ${playfair.variable}`}>
      <body className="font-sans">{children}</body>
    </html>
  )
}

// tailwind.config.ts
fontFamily: {
  sans: ["var(--font-inter)"],
  display: ["var(--font-playfair)"],
}
```

### 11. Environment Variables

```bash
# .env.local (not committed)
DATABASE_URL="..."
API_SECRET="..."

# .env (committed, public defaults)
NEXT_PUBLIC_SITE_URL="https://example.com"
```

```tsx
// Server-side only
const dbUrl = process.env.DATABASE_URL

// Client-side accessible (must have NEXT_PUBLIC_ prefix)
const siteUrl = process.env.NEXT_PUBLIC_SITE_URL
```

### 12. Middleware

```tsx
// middleware.ts (at root)
import { NextResponse } from "next/server"
import type { NextRequest } from "next/server"

export function middleware(request: NextRequest) {
  // Redirect www to non-www
  if (request.headers.get("host")?.startsWith("www.")) {
    return NextResponse.redirect(
      new URL(request.url.replace("www.", ""))
    )
  }

  // Add security headers
  const response = NextResponse.next()
  response.headers.set("X-Frame-Options", "DENY")
  return response
}

export const config = {
  matcher: [
    "/((?!api|_next/static|_next/image|favicon.ico).*)",
  ],
}
```

### 13. Static Export

For static hosting (no server):

```js
// next.config.js
module.exports = {
  output: "export",
  images: {
    unoptimized: true, // Required for static export
  },
}
```

## Performance Checklist

- [ ] Images use next/image with proper sizes
- [ ] Fonts use next/font with display: swap
- [ ] Third-party scripts use next/script
- [ ] Heavy components use dynamic imports
- [ ] Data fetched in parallel where possible
- [ ] Proper caching headers set
- [ ] Static pages pre-rendered
- [ ] Bundle size monitored (@next/bundle-analyzer)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/deomiarn) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
