---
name: nextjs-fullstack
description: Complete Next.js 16 full-stack development guide with App Router, Cache Components, Partial Prerendering, TypeScript, Tailwind CSS, shadcn/ui, Prisma, and state management. Use when building web applications, APIs, components, or full features in Next.js. Includes token-saving patterns and efficient development workflows. Use when this capability is needed.
metadata:
  author: barisariburnu
---

# Next.js Full-Stack Development Skill (Next.js 16)

**Skill Location**: `{project_path}/skills/nextjs-fullstack/`

Comprehensive guide for efficient Next.js 16 development with App Router and Cache Components, optimized for minimal token usage while maintaining production-quality code.

## What's New in Next.js 16

- **Cache Components**: New caching architecture with `use cache` directive
- **Partial Prerendering (PPR)**: Static shell + dynamic content in same route
- **use cache: private**: User-specific caching
- **use cache: remote**: Remote cache support (Redis, etc.)
- **updateTag() API**: Granular cache invalidation
- **Opt-in caching**: Explicit caching control, no automatic cache magic
- **React Compiler**: Improved performance with built-in optimization

---

## When to Use This Skill (Trigger Patterns)

**MUST apply this skill when:**
- Building Next.js applications with App Router
- Creating React components and pages
- Implementing API routes and backend logic
- Setting up database operations with Prisma
- Managing application state (Zustand, React Query)
- Working with shadcn/ui components
- Optimizing performance and SEO

**Trigger phrases:**
- "build a Next.js app"
- "create a page/component"
- "add an API route"
- "integrate with database"
- "implement authentication"
- "optimize performance"

---

## Core Tech Stack (Non-Negotiable)

```
Framework: Next.js 16 (App Router + Cache Components)
Language: TypeScript 5
Styling: Tailwind CSS 4
UI Components: shadcn/ui (New York style)
ORM: Prisma (SQLite)
State: Zustand (client), TanStack Query (server)
Icons: Lucide React
Validation: Zod
Authentication: NextAuth.js v4
Caching: Next.js 16 Cache Components
```

## Next.js 16 Configuration

### Enable Cache Components

```typescript
// next.config.ts
const nextConfig = {
  experimental: {
    cacheComponents: true,  // Enable Cache Components architecture
  },
  reactCompiler: true,  // Enable React Compiler
}

export default nextConfig
```

---

## Token-Saving Strategies (CRITICAL)

### 1. Use Project Conventions First

**❌ INEFFICIENT:** Explaining basic concepts
```
"You need to import React because it's a library that..."
```

**✅ EFFICIENT:** Assume knowledge, focus on implementation
```
// Component follows existing patterns in src/components/ui
// Use established types from @/types
```

### 2. Reference Existing Code Instead of Repeating

**❌ INEFFICIENT:** Full code every time
```typescript
// Writing complete Button component every time
const Button = ({ children, onClick, variant = 'primary' }) => { ... }
```

**✅ EFFICIENT:** Reference existing components
```typescript
// Use existing Button from @/components/ui/button
import { Button } from '@/components/ui/button'

<Button variant="primary" onClick={handleClick}>
  {label}
</Button>
```

### 3. Use Shorthand for Common Patterns

**Abbreviations to use:**
- `use client` → "client component" (unless writing code)
- `use server` → "server action/API route" (unless writing code)
- `async/await` → "async operation" (unless complex)
- `try/catch` → "with error handling" (unless complex)

### 4. Minimal Comments in Code

**❌ INEFFICIENT:**
```typescript
// This function handles the submit event
// It validates the form data
// Then sends it to the API
const handleSubmit = async (e: React.FormEvent) => { ... }
```

**✅ EFFICIENT:**
```typescript
// Form submit with validation and API call
const handleSubmit = async (e: React.FormEvent) => { ... }
```

---

## Project Structure Convention

```
src/
├── app/
│   ├── (auth)/           # Auth routes group
│   │   ├── login/page.tsx
│   │   └── register/page.tsx
│   ├── (dashboard)/      # Dashboard routes
│   │   └── page.tsx
│   ├── api/              # API routes
│   │   └── [resource]/
│   │       └── route.ts
│   ├── layout.tsx        # Root layout
│   └── page.tsx          # Home page
├── components/
│   ├── ui/               # shadcn/ui components (DON'T modify)
│   └── features/         # Feature components
│       └── user/
│           ├── UserCard.tsx
│           └── UserForm.tsx
├── lib/
│   ├── db.ts             # Prisma client
│   ├── utils.ts          # Utility functions
│   └── validations.ts    # Zod schemas
├── hooks/
│   └── use-*.ts          # Custom hooks
├── store/
│   └── *.ts              # Zustand stores
├── types/
│   └── *.ts              # TypeScript types
└── styles/
    └── globals.css       # Global styles
```

---

## Quick Reference Patterns

### Cache Components (Next.js 16)

```typescript
// Use cache directive at file level
'use cache'

export async function CachedData() {
  const data = await db.post.findMany()
  return <div>{/* Cached content */}</div>
}
```

```typescript
// Private cache (user-specific)
'use cache: private'

export async function UserPreferences() {
  const session = await getServerSession()
  const preferences = await db.preferences.findUnique({
    where: { userId: session.user.id }
  })
  return <div>{/* Per-user cached */}</div>
}
```

```typescript
// Remote cache (Redis, etc.)
'use cache: remote'

export async function RemoteCachedData() {
  const data = await fetch('https://api.example.com/data').then(r => r.json())
  return <div>{/* Remote cached */}</div>
}
```

```typescript
// Cache invalidation with updateTag
'use cache'

import { updateTag } from 'next/cache'

export async function CachedList() {
  const items = await db.item.findMany()

  revalidateTag('items')

  return <div>{/* Items */}</div>
}

// Invalidate from another route
export async function POST(req: NextRequest) {
  await db.item.create({ data: await req.json() })
  updateTag('items')  // Invalidate cached list
  return NextResponse.json({ success: true })
}
```

### Partial Prerendering (PPR)

```typescript
// Page with static shell + dynamic content
export default function Page() {
  return (
    <div>
      {/* Static shell - pre-rendered */}
      <header>Static Header</header>

      {/* Dynamic content - rendered at request time */}
      <Suspense fallback={<div>Loading...</div>}>
        <DynamicComponent />
      </Suspense>

      {/* Static footer - pre-rendered */}
      <footer>Static Footer</footer>
    </div>
  )
}

// Enable PPR for specific route
export const dynamic = 'force-dynamic'
```

### Component Template

```typescript
'use client'

import { useState } from 'react'
import { Button } from '@/components/ui/button'

interface Props {
  onAction: (data: string) => void
}

export function ComponentName({ onAction }: Props) {
  const [loading, setLoading] = useState(false)

  const handleClick = async () => {
    setLoading(true)
    try {
      const response = await fetch('/api/action', { method: 'POST' })
    } catch (error) {
      // Error handling
    } finally {
      setLoading(false)
    }
  }

  return (
    <div>
      <Button onClick={handleClick} disabled={loading}>
        {loading ? 'Loading...' : 'Action'}
      </Button>
    </div>
  )
}
```

### API Route Template

```typescript
import { NextRequest, NextResponse } from 'next/server'
import { db } from '@/lib/db'
import { z } from 'zod'

const schema = z.object({
  name: z.string().min(1),
  email: z.string().email()
})

export async function POST(req: NextRequest) {
  try {
    const body = await req.json()
    const data = schema.parse(body)

    const result = await db.user.create({
      data
    })

    return NextResponse.json(result)
  } catch (error) {
    if (error instanceof z.ZodError) {
      return NextResponse.json(
        { error: error.errors },
        { status: 400 }
      )
    }
    return NextResponse.json(
      { error: 'Internal error' },
      { status: 500 }
    )
  }
}
```

### Prisma Query Patterns

```typescript
// Single record
const user = await db.user.findUnique({
  where: { id: params.id }
})

// List with pagination
const users = await db.user.findMany({
  skip: (page - 1) * limit,
  take: limit,
  orderBy: { createdAt: 'desc' }
})

// With relations
const posts = await db.post.findMany({
  include: {
    author: { select: { id: true, name: true } }
  }
})

// Transaction
await db.$transaction([
  db.user.delete({ where: { id } }),
  db.post.deleteMany({ where: { authorId: id } })
])
```

---

## Common Development Workflows

### 1. Adding a New Feature

**Steps:**
1. Define types in `src/types/`
2. Create Prisma schema/model if needed
3. Build API routes in `src/app/api/`
4. Create UI components in `src/components/features/`
5. Create page/route in `src/app/`
6. Add state management if needed

**Token-Saving Prompt:**
```
Create user management feature:
- List users (GET /api/users)
- Create user (POST /api/users)
- Delete user (DELETE /api/users/[id])
- Use existing components from shadcn/ui
- Follow project structure
```

### 2. Adding a New Page

**Template:**
```typescript
// src/app/page-name/page.tsx
import { Suspense } from 'react'

export default function PageName() {
  return (
    <div className="container mx-auto py-6">
      <h1 className="text-3xl font-bold mb-6">Page Title</h1>
      <Suspense fallback={<div>Loading...</div>}>
        {/* Async content */}
      </Suspense>
    </div>
  )
}
```

### 3. Form with Validation

```typescript
'use client'

import { useForm } from 'react-hook-form'
import { zodResolver } from '@hookform/resolvers/zod'
import { z } from 'zod'
import { Form, FormControl, FormField, FormItem, FormLabel, FormMessage } from '@/components/ui/form'

const schema = z.object({
  name: z.string().min(2, 'Name required'),
  email: z.string().email('Invalid email')
})

export function UserForm() {
  const form = useForm<z.infer<typeof schema>>({
    resolver: zodResolver(schema)
  })

  const onSubmit = async (data: z.infer<typeof schema>) => {
    await fetch('/api/users', {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify(data)
    })
  }

  return (
    <Form {...form}>
      <form onSubmit={form.handleSubmit(onSubmit)} className="space-y-4">
        {/* Form fields */}
      </form>
    </Form>
  )
}
```

### 4. Server Component with Data Fetching

```typescript
import { db } from '@/lib/db'

export default async function UserPage({
  params
}: {
  params: { id: string }
}) {
  const user = await db.user.findUnique({
    where: { id: params.id },
    include: { posts: true }
  })

  if (!user) {
    return <div>User not found</div>
  }

  return (
    <div>
      <h1>{user.name}</h1>
      {/* Render posts */}
    </div>
  )
}
```

### 5. Client State Management with Zustand

```typescript
// src/store/user-store.ts
import { create } from 'zustand'

interface UserStore {
  user: User | null
  setUser: (user: User | null) => void
  clearUser: () => void
}

export const useUserStore = create<UserStore>((set) => ({
  user: null,
  setUser: (user) => set({ user }),
  clearUser: () => set({ user: null })
}))

// Usage in component
function UserProfile() {
  const { user, setUser } = useUserStore()
  // ...
}
```

### 6. Server State with TanStack Query

```typescript
'use client'

import { useQuery, useMutation, useQueryClient } from '@tanstack/react-query'

function UsersList() {
  const queryClient = useQueryClient()

  // Fetch
  const { data: users, isLoading } = useQuery({
    queryKey: ['users'],
    queryFn: () => fetch('/api/users').then(r => r.json())
  })

  // Mutate
  const mutation = useMutation({
    mutationFn: (data: UserCreate) =>
      fetch('/api/users', {
        method: 'POST',
        body: JSON.stringify(data)
      }).then(r => r.json()),
    onSuccess: () => {
      queryClient.invalidateQueries({ queryKey: ['users'] })
    }
  })

  if (isLoading) return <div>Loading...</div>
  return <div>{/* Render users */}</div>
}
```

---

## shadcn/ui Component Usage

### Quick Reference (Don't Recreate These)

**Available components** (use directly from `@/components/ui/`):
- Button, Input, Textarea
- Card, Avatar, Badge
- Dialog, Sheet, Drawer
- Form, Select, Checkbox
- Table, Tabs, Accordion
- Toast, Alert, Skeleton
- Calendar, DatePicker
- And 50+ more components

**Usage Pattern:**
```typescript
import { Button } from '@/components/ui/button'
import { Card, CardContent, CardHeader, CardTitle } from '@/components/ui/card'
import { Input } from '@/components/ui/input'

// Build UI from these components
// No need to create new ones unless feature-specific
```

---

## Error Handling Patterns

### API Error Response

```typescript
class ApiError extends Error {
  constructor(
    public message: string,
    public status: number,
    public code?: string
  ) {
    super(message)
    this.name = 'ApiError'
  }
}

// Usage
if (!user) {
  throw new ApiError('User not found', 404, 'USER_NOT_FOUND')
}
```

### Client Error Handling

```typescript
const fetchData = async () => {
  try {
    const res = await fetch('/api/data')
    if (!res.ok) {
      const error = await res.json()
      throw new Error(error.message || 'Request failed')
    }
    return await res.json()
  } catch (error) {
    console.error('Fetch error:', error)
    // Show toast or error message
  }
}
```

---

## Performance Optimization

### 1. Dynamic Imports for Large Components

```typescript
import dynamic from 'next/dynamic'

const HeavyComponent = dynamic(
  () => import('@/components/features/HeavyComponent'),
  { loading: () => <div>Loading...</div> }
)
```

### 2. Image Optimization

```typescript
import Image from 'next/image'

<Image
  src="/avatar.jpg"
  alt="User avatar"
  width={100}
  height={100}
  priority // For above-fold images
/>
```

### 3. Caching API Routes

```typescript
export async function GET(req: NextRequest) {
  return NextResponse.json(data, {
    headers: {
      'Cache-Control': 'public, s-maxage=60, stale-while-revalidate=300'
    }
  })
}
```

---

## Common Pitfalls & Solutions

### ❌ Problem: Using Client Components When Server is Better

**Scenario:** Fetching data and displaying it

```typescript
// Bad: Unnecessary client component
'use client'
const [data, setData] = useState(null)
useEffect(() => { fetchData() }, [])
```

**✅ Solution:** Use server component

```typescript
// Good: Server component fetches directly
export default async function Page() {
  const data = await db.user.findMany()
  return <div>{/* Render */}</div>
}
```

### ❌ Problem: Hardcoding Repeated Logic

**✅ Solution:** Create utility functions

```typescript
// src/lib/utils.ts
export async function fetchData<T>(url: string): Promise<T> {
  const res = await fetch(url)
  if (!res.ok) throw new Error('Fetch failed')
  return res.json()
}

// Usage everywhere
const users = await fetchData<User[]>('/api/users')
```

### ❌ Problem: Not Following TypeScript Conventions

**✅ Solution:** Use consistent types

```typescript
// src/types/index.ts
export interface User {
  id: string
  name: string
  email: string
  createdAt: Date
}

export interface UserCreate {
  name: string
  email: string
}

// Type inference from Prisma
export type UserWithPosts = Prisma.UserGetPayload<{
  include: { posts: true }
}>
```

---

## Development Workflow Checklist

### Before Coding
- [ ] Check if similar code exists in the project
- [ ] Identify reusable components from shadcn/ui
- [ ] Define TypeScript types/interfaces
- [ ] Plan data flow (client/server)

### During Coding
- [ ] Follow project structure conventions
- [ ] Use existing utilities and helpers
- [ ] Minimize comments (code should be self-documenting)
- [ ] Handle errors appropriately

### After Coding
- [ ] Run `bun run lint` to check code quality
- [ ] Verify types with TypeScript
- [ ] Test in development environment
- [ ] Check dev logs at `/home/z/my-project/dev.log`

---

## Token-Efficient Prompt Templates

### Feature Development
```
Add <FEATURE>:
- Follow project structure in src/
- Use shadcn/ui components from @/components/ui/
- API route: POST/GET/PUT/DELETE
- Include validation with Zod
- Reference existing patterns for consistency
```

### Bug Fix
```
Fix issue: <DESCRIPTION>
- Location: <FILE_PATH>
- Use existing error handling patterns
- Maintain type safety
- Test with edge cases
```

### Optimization
```
Optimize <COMPONENT/PAGE>:
- Use server components where possible
- Implement caching for API routes
- Dynamic import heavy components
- Follow performance best practices
```

---

## Quick Command Reference

```bash
# Development
bun run dev          # Start dev server
bun run lint         # Check code quality
bun run build        # Build for production

# Database
bun run db:push      # Push schema to database
bun run db:studio    # Open Prisma Studio

# Code Generation
bunx shadcn-ui@latest add <component>  # Add shadcn/ui component
```

---

## Important Reminders

1. **Always read dev.log** after coding: `/home/z/my-project/dev.log`
2. **Use existing components** - don't recreate shadcn/ui components
3. **Follow project structure** - maintain consistency
4. **Minimize tokens** - use conventions and abbreviations
5. **API over Server Actions** - prefer API routes
6. **Sticky footer** - use `min-h-screen flex flex-col` with `mt-auto` on footer
7. **Responsive design** - mobile-first with Tailwind breakpoints

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/barisariburnu) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
