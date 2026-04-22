---
name: performance-optimization
description: Complete performance optimization guide for Next.js applications including frontend, backend, database, caching, and general optimization strategies. Use when improving performance, reducing bundle size, optimizing queries, or implementing caching. Use when this capability is needed.
metadata:
  author: barisariburnu
---

# Performance Optimization Skill

**Skill Location**: `{project_path}/skills/performance-optimization/`

Comprehensive guide for optimizing Next.js applications, optimized for minimal token usage while covering all performance aspects.

---

## When to Use This Skill (Trigger Patterns)

**MUST apply this skill when:**
- Optimizing page load times
- Reducing bundle size
- Improving database query performance
- Implementing caching strategies
- Optimizing API responses
- Reducing client-side JavaScript
- Improving Core Web Vitals

**Trigger phrases:**
- "optimize performance"
- "reduce bundle size"
- "improve load time"
- "make it faster"
- "implement caching"
- "optimize database queries"

---

## Performance Metrics

```
Core Web Vitals:
- LCP (Largest Contentful Paint): < 2.5s
- FID (First Input Delay): < 100ms
- CLS (Cumulative Layout Shift): < 0.1
- FCP (First Contentful Paint): < 1.8s
- TTI (Time to Interactive): < 3.8s
```

---

## Token-Saving Strategies

### 1. Focus on Specific Optimizations

**❌ INEFFICIENT:**
```
"Performance is important because users expect fast loading..."
```

**✅ EFFICIENT:**
```
// Dynamic import for code splitting
```

### 2. Use Standard Patterns

Don't explain:
- Why optimization is important
- What LCP, FID, CLS mean
- Basic performance concepts

Focus on:
- Implementation patterns
- Specific optimizations
- Tool configurations

### 3. Code Examples Over Explanations

Show code, not theory.

---

## Frontend Optimization

### 1. Code Splitting

```typescript
// Dynamic imports for heavy components
import dynamic from 'next/dynamic'

const ChartComponent = dynamic(
  () => import('@/components/features/Chart'),
  { loading: () => <div>Loading chart...</div> }
)

const HeavyModal = dynamic(
  () => import('@/components/features/HeavyModal'),
  { ssr: false } // Don't render on server
)

// Route-based code splitting (automatic)
// Each page in /app is automatically split
```

### 2. Image Optimization

```typescript
import Image from 'next/image'

// Optimized image
<Image
  src="/hero.jpg"
  alt="Hero section"
  width={1920}
  height={1080}
  priority // For above-fold images
  quality={85}
  placeholder="blur"
  blurDataURL="data:image/..." // Base64 placeholder
/>

// Responsive images
<Image
  src="/image.jpg"
  alt="Responsive"
  width={800}
  height={600}
  sizes="(max-width: 768px) 100vw, (max-width: 1200px) 50vw, 33vw"
  priority
/>
```

### 3. Font Optimization

```typescript
// next.config.ts
module.exports = {
  optimizeFonts: true
}

// Use next/font
import { Inter } from 'next/font/google'

const inter = Inter({
  subsets: ['latin'],
  display: 'swap',
  variable: '--font-inter'
})

export default function RootLayout({
  children
}: {
  children: React.ReactNode
}) {
  return (
    <html lang="en" className={inter.variable}>
      <body>{children}</body>
    </html>
  )
}
```

### 4. Lazy Loading Components

```typescript
// Intersection Observer for lazy loading
'use client'

import { useRef, useEffect, useState } from 'react'

function LazyComponent({ children }: { children: React.ReactNode }) {
  const [isVisible, setIsVisible] = useState(false)
  const ref = useRef<HTMLDivElement>(null)

  useEffect(() => {
    const observer = new IntersectionObserver(([entry]) => {
      if (entry.isIntersecting) {
        setIsVisible(true)
        observer.disconnect()
      }
    })

    if (ref.current) {
      observer.observe(ref.current)
    }

    return () => observer.disconnect()
  }, [])

  return (
    <div ref={ref}>
      {isVisible ? children : <div>Loading...</div>}
    </div>
  )
}
```

### 5. Memoization

```typescript
import { useMemo, useCallback } from 'react'

// Expensive calculations
function ExpensiveComponent({ data }: { data: number[] }) {
  const sum = useMemo(
    () => data.reduce((a, b) => a + b, 0),
    [data]
  )
  return <div>{sum}</div>
}

// Prevent unnecessary re-renders
function Parent({ items }: { items: Item[] }) {
  const handleClick = useCallback((id: string) => {
    console.log('Clicked:', id)
  }, [])

  return (
    <>
      {items.map(item => (
        <Child key={item.id} item={item} onClick={handleClick} />
      ))}
    </>
  )
}

// Memo component
import { memo } from 'react'

const Child = memo(function Child({
  item,
  onClick
}: {
  item: Item
  onClick: (id: string) => void
}) {
  return <button onClick={() => onClick(item.id)}>{item.name}</button>
})
```

---

## Backend Optimization

### 1. Server Components

```typescript
// Server components - no JS sent to client
export default async function UserList() {
  const users = await db.user.findMany()

  return (
    <div>
      {users.map(user => (
        <div key={user.id}>{user.name}</div>
      ))}
    </div>
  )
}

// Split into server and client parts
// server.tsx
export async function ServerComponent() {
  const data = await fetchData()
  return <ClientComponent data={data} />
}

// client.tsx
'use client'
export function ClientComponent({ data }: { data: Data }) {
  // Interactive code here
}
```

### 2. API Response Optimization

```typescript
// Select only needed fields
export async function GET(req: NextRequest) {
  const users = await db.user.findMany({
    select: {
      id: true,
      name: true,
      email: true
      // Don't select heavy fields
    }
  })

  return NextResponse.json(users)
}

// Pagination
export async function GET(req: NextRequest) {
  const { searchParams } = new URL(req.url)
  const page = parseInt(searchParams.get('page') || '1')
  const limit = parseInt(searchParams.get('limit') || '10')

  const [users, total] = await Promise.all([
    db.user.findMany({
      skip: (page - 1) * limit,
      take: limit
    }),
    db.user.count()
  ])

  return NextResponse.json({
    users,
    pagination: { page, limit, total }
  })
}
```

### 3. Caching

```typescript
// Static generation
export const revalidate = 3600 // 1 hour

export async function GET() {
  const data = await fetchData()
  return NextResponse.json(data)
}

// Static export for public pages
export const dynamic = 'force-static'

// Incremental static regeneration
export const revalidate = 60 // Revalidate every 60 seconds

// Cache control headers
export async function GET() {
  return NextResponse.json(data, {
    headers: {
      'Cache-Control': 'public, s-maxage=3600, stale-while-revalidate=7200'
    }
  })
}
```

---

## Database Optimization

### 1. Query Optimization

```typescript
// Use indexes
// prisma/schema.prisma
model User {
  id    String @id @default(cuid())
  email String @unique
  name  String

  @@index([email])
  @@index([name])
}

// Select only needed fields
const users = await db.user.findMany({
  select: {
    id: true,
    name: true
    // Don't select heavy fields
  }
})

// Use relations instead of separate queries
const posts = await db.post.findMany({
  include: {
    author: {
      select: {
        id: true,
        name: true
      }
    }
  }
})

// Use pagination
const users = await db.user.findMany({
  skip: (page - 1) * limit,
  take: limit
})
```

### 2. Connection Pooling

```typescript
// Prisma connection pool (automatic)
// Configure in .env
DATABASE_URL="file:./dev.db?connection_limit=10"

// Use transactions for multiple operations
await db.$transaction([
  db.order.create({ data: orderData }),
  db.product.updateMany({
    where: { id: { in: productIds } },
    data: { stock: { decrement: 1 } }
  })
])
```

### 3. Batch Operations

```typescript
// Create many instead of individual creates
await db.user.createMany({
  data: [
    { name: 'John', email: 'john@example.com' },
    { name: 'Jane', email: 'jane@example.com' }
  ],
  skipDuplicates: true
})

// Update many
await db.user.updateMany({
  where: { status: 'inactive' },
  data: { isDeleted: true }
})
```

---

## State Management Optimization

### 1. Server State with TanStack Query

```typescript
// Cache and deduplicate requests
function UsersList() {
  const { data: users, isLoading } = useQuery({
    queryKey: ['users'],
    queryFn: () => fetch('/api/users').then(r => r.json()),
    staleTime: 60 * 1000, // 1 minute
    cacheTime: 5 * 60 * 1000 // 5 minutes
  })

  if (isLoading) return <div>Loading...</div>
  return <div>{/* Render */}</div>
}
```

### 2. Client State with Zustand

```typescript
// Minimal state, only what's needed
interface UserStore {
  user: User | null
  setUser: (user: User | null) => void
}

export const useUserStore = create<UserStore>((set) => ({
  user: null,
  setUser: (user) => set({ user })
}))
```

---

## Asset Optimization

### 1. CSS Optimization

```css
/* Use CSS variables for theming */
:root {
  --color-primary: #000;
  --spacing-sm: 8px;
}

/* Tailwind CSS purges unused styles in production */
/* next.config.ts */
module.exports = {
  tailwindcss: {
    content: ['./src/**/*.{js,ts,jsx,tsx}']
  }
}
```

### 2. Bundle Analysis

```typescript
// next.config.ts
const withBundleAnalyzer = require('@next/bundle-analyzer')({
  enabled: process.env.ANALYZE === 'true'
})

module.exports = withBundleAnalyzer({
  // ...config
})

// Analyze bundle
bun run build
ANALYZE=true bun run build
```

### 3. Tree Shaking

```typescript
// Import only what you need
// Bad
import _ from 'lodash'

// Good
import debounce from 'lodash/debounce'

// Better - use native or smaller libraries
import { debounce } from '@/lib/utils'
```

---

## Common Optimizations

### 1. Debounce and Throttle

```typescript
// Debounce
function debounce<T extends (...args: any[]) => any>(
  func: T,
  wait: number
): (...args: Parameters<T>) => void {
  let timeout: NodeJS.Timeout
  return (...args: Parameters<T>) => {
    clearTimeout(timeout)
    timeout = setTimeout(() => func(...args), wait)
  }
}

// Throttle
function throttle<T extends (...args: any[]) => any>(
  func: T,
  limit: number
): (...args: Parameters<T>) => void {
  let inThrottle: boolean
  return (...args: Parameters<T>) => {
    if (!inThrottle) {
      func(...args)
      inThrottle = true
      setTimeout(() => (inThrottle = false), limit)
    }
  }
}

// Usage
const debouncedSearch = debounce((query: string) => {
  searchUsers(query)
}, 300)

<input onChange={(e) => debouncedSearch(e.target.value)} />
```

### 2. Virtualization

```typescript
// Use react-window or react-virtualized for long lists
import { FixedSizeList } from 'react-window'

function VirtualList({ items }: { items: any[] }) {
  const Row = ({ index, style }: { index: number; style: any }) => (
    <div style={style}>{items[index].name}</div>
  )

  return (
    <FixedSizeList
      height={400}
      itemCount={items.length}
      itemSize={50}
      width="100%"
    >
      {Row}
    </FixedSizeList>
  )
}
```

### 3. Lazy Loading Images

```typescript
'use client'

import { useState, useRef } from 'react'

function LazyImage({ src, alt }: { src: string; alt: string }) {
  const [isLoaded, setIsLoaded] = useState(false)
  const imgRef = useRef<HTMLImageElement>(null)

  return (
    <img
      ref={imgRef}
      src={src}
      alt={alt}
      loading="lazy"
      onLoad={() => setIsLoaded(true)}
      style={{ opacity: isLoaded ? 1 : 0, transition: 'opacity 0.3s' }}
    />
  )
}
```

---

## Performance Monitoring

### 1. Core Web Vitals

```typescript
'use client'

import { useReportWebVitals } from 'next/web-vitals'

export function WebVitals() {
  useReportWebVitals((metric) => {
    // Send to analytics
    console.log(metric)

    if (metric.rating === 'poor') {
      // Log poor performance
      console.warn(`${metric.name} is poor: ${metric.value}`)
    }
  })

  return null
}

// In app/layout.tsx
import { WebVitals } from '@/components/WebVitals'

export default function RootLayout({ children }) {
  return (
    <html>
      <head />
      <body>
        <WebVitals />
        {children}
      </body>
    </html>
  )
}
```

### 2. Performance API

```typescript
// Measure performance
function measurePerformance() {
  const startTime = performance.now()

  // Do something
  expensiveOperation()

  const endTime = performance.now()
  console.log(`Operation took ${endTime - startTime}ms`)
}
```

---

## Common Pitfalls & Solutions

### ❌ Problem: Large initial bundle

**✅ Solution:** Code splitting and dynamic imports

```typescript
import dynamic from 'next/dynamic'

const HeavyComponent = dynamic(
  () => import('@/components/Heavy'),
  { ssr: false }
)
```

### ❌ Problem: N+1 query problem

**✅ Solution:** Use include/select or batch queries

```typescript
// Bad
const posts = await db.post.findMany()
for (const post of posts) {
  const author = await db.user.findUnique({ where: { id: post.authorId }})
}

// Good
const posts = await db.post.findMany({
  include: { author: true }
})
```

### ❌ Problem: Unnecessary re-renders

**✅ Solution:** Use memo and useCallback

```typescript
const memoizedValue = useMemo(() => expensiveCalculation(data), [data])
const memoizedCallback = useCallback(() => handleEvent(id), [id])
```

### ❌ Problem: Large images

**✅ Solution:** Use next/image with optimization

```typescript
<Image
  src="/image.jpg"
  alt="Description"
  width={800}
  height={600}
  quality={85}
  priority
/>
```

---

## Token-Efficient Prompt Templates

### Optimize Page
```
Optimize <PAGE>:
- Dynamic imports for heavy components
- Image optimization with next/image
- Server components for static content
- Implement caching
```

### Optimize Database
```
Optimize database queries:
- Add indexes for frequent queries
- Select only needed fields
- Use pagination
- Batch operations
```

### Reduce Bundle
```
Reduce bundle size:
- Dynamic imports
- Tree shaking
- Remove unused dependencies
- Analyze bundle with @next/bundle-analyzer
```

---

## Quick Commands

```typescript
// Analyze bundle
ANALYZE=true bun run build

// Check bundle size
bun run build
# Check output for bundle sizes

// Performance testing
# Use Lighthouse Chrome extension
# Use PageSpeed Insights: https://pagespeed.web.dev/

// Database query analysis
bunx prisma studio
# Check query performance
```

---

## Optimization Checklist

### Frontend
- [ ] Code splitting for heavy components
- [ ] Image optimization with next/image
- [ ] Font optimization with next/font
- [ ] Memoization for expensive operations
- [ ] Virtualization for long lists
- [ ] Lazy loading for images and components
- [ ] CSS optimization and purging

### Backend
- [ ] Server components where possible
- [ ] API response optimization
- [ ] Caching strategies
- [ ] Static generation for static pages
- [ ] Incremental static regeneration

### Database
- [ ] Indexes for frequent queries
- [ ] Select only needed fields
- [ ] Pagination for large datasets
- [ ] Batch operations
- [ ] Query optimization

### General
- [ ] Monitor Core Web Vitals
- [ ] Regular bundle analysis
- [ ] Performance monitoring
- [ ] Regular performance audits

---

## Important Reminders

1. **Measure first** - Profile before optimizing
2. **Critical path** - Optimize what matters most
3. **Server components** - Default to server components
4. **Code splitting** - Use dynamic imports
5. **Image optimization** - Always use next/image
6. **Database indexes** - Add for frequent queries
7. **Caching** - Cache where possible
8. **Monitor** - Track performance over time

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/barisariburnu) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
