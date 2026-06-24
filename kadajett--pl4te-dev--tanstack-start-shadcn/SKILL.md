---
name: tanstack-start-shadcn
description: Build full-stack React applications with TanStack Start, React Query, shadcn/ui, and Tailwind CSS v4. Use when working on projects created with create-tanstack-start-shadcn, or when the user needs help with TanStack Start patterns, server functions, React Query integration, or shadcn/ui components. Use when this capability is needed.
metadata:
  author: kadajett
---

# TanStack Start + shadcn/ui Development Guide

Comprehensive guide for developing with the `create-tanstack-start-shadcn` boilerplate.

## Quick Reference

| Task | Command/Pattern |
|------|-----------------|
| Start dev server | `npm run dev` |
| Build for production | `npm run build` |
| Preview production | `npm run preview` |
| Start production | `npm run start` |
| Add shadcn component | `npx shadcn@latest add <component>` |

## Project Structure

```
my-app/
├── src/
│   ├── components/           # React components
│   │   ├── ui/               # shadcn/ui components
│   │   ├── app-sidebar.tsx   # Navigation sidebar
│   │   ├── DefaultCatchBoundary.tsx
│   │   └── NotFound.tsx
│   ├── hooks/                # Custom React hooks
│   ├── lib/                  # Utility functions
│   │   └── utils.ts          # cn() helper for classNames
│   ├── routes/               # File-based routes
│   │   ├── __root.tsx        # Root layout (HTML shell)
│   │   ├── index.tsx         # Home page (/)
│   │   ├── posts.tsx         # Layout route (/posts)
│   │   ├── posts.$postId.tsx # Dynamic route (/posts/:postId)
│   │   ├── api/              # API routes
│   │   │   └── users.ts      # REST endpoint (/api/users)
│   │   └── _pathlessLayout/  # Pathless layout groups
│   ├── styles/
│   │   └── app.css           # Tailwind CSS + theme variables
│   ├── utils/                # Data fetching & utilities
│   │   ├── posts.tsx         # Posts server functions + query options
│   │   ├── users.tsx         # Users server functions + query options
│   │   └── seo.ts            # SEO meta tag helper
│   ├── router.tsx            # Router configuration
│   └── routeTree.gen.ts      # Auto-generated route tree
├── public/                   # Static assets
├── components.json           # shadcn/ui configuration
├── vite.config.ts            # Vite + TanStack Start config
└── tsconfig.json
```

## Adding New Routes

### Basic Page Route

Create a new file in `src/routes/`:

```tsx
// src/routes/about.tsx
import { createFileRoute } from '@tanstack/react-router'

export const Route = createFileRoute('/about')({
  component: AboutPage,
})

function AboutPage() {
  return (
    <div className="p-6">
      <h1 className="text-2xl font-bold">About</h1>
    </div>
  )
}
```

### Dynamic Route with Parameters

```tsx
// src/routes/users.$userId.tsx
import { createFileRoute } from '@tanstack/react-router'

export const Route = createFileRoute('/users/$userId')({
  component: UserPage,
})

function UserPage() {
  const { userId } = Route.useParams()
  return <div>User ID: {userId}</div>
}
```

### Layout Route (with Outlet)

```tsx
// src/routes/dashboard.tsx
import { createFileRoute, Outlet } from '@tanstack/react-router'

export const Route = createFileRoute('/dashboard')({
  component: DashboardLayout,
})

function DashboardLayout() {
  return (
    <div className="flex">
      <nav className="w-48 border-r">
        {/* Sidebar navigation */}
      </nav>
      <main className="flex-1">
        <Outlet /> {/* Child routes render here */}
      </main>
    </div>
  )
}
```

### Pathless Layout (URL-invisible grouping)

Use `_` prefix for layouts that don't add URL segments:

```tsx
// src/routes/_authenticated.tsx
import { createFileRoute, Outlet, redirect } from '@tanstack/react-router'

export const Route = createFileRoute('/_authenticated')({
  beforeLoad: ({ context }) => {
    if (!context.auth) {
      throw redirect({ to: '/login' })
    }
  },
  component: () => <Outlet />,
})

// src/routes/_authenticated/settings.tsx → /settings (not /_authenticated/settings)
```

## React Query Integration

This boilerplate uses the recommended TanStack Router + React Query integration pattern.

### Query Options Pattern (Recommended)

Define reusable query options in `src/utils/`:

```tsx
// src/utils/products.tsx
import { queryOptions } from '@tanstack/react-query'
import { createServerFn } from '@tanstack/react-start'

export type Product = {
  id: number
  name: string
  price: number
}

// Server function for fetching
export const fetchProducts = createServerFn().handler(async () => {
  const res = await fetch('https://api.example.com/products')
  if (!res.ok) throw new Error('Failed to fetch products')
  return res.json() as Promise<Product[]>
})

export const fetchProduct = createServerFn({ method: 'POST' })
  .inputValidator((id: string) => id)
  .handler(async ({ data: id }) => {
    const res = await fetch(`https://api.example.com/products/${id}`)
    if (!res.ok) throw new Error('Failed to fetch product')
    return res.json() as Promise<Product>
  })

// Query options (reusable)
export const productsQueryOptions = () =>
  queryOptions({
    queryKey: ['products'],
    queryFn: () => fetchProducts(),
  })

export const productQueryOptions = (id: string) =>
  queryOptions({
    queryKey: ['product', id],
    queryFn: () => fetchProduct({ data: id }),
  })
```

### Using in Routes (SSR-compatible)

```tsx
// src/routes/products.tsx
import { createFileRoute } from '@tanstack/react-router'
import { useSuspenseQuery } from '@tanstack/react-query'
import { productsQueryOptions } from '../utils/products'

export const Route = createFileRoute('/products')({
  // Pre-fetch in loader (runs on server during SSR)
  loader: ({ context }) =>
    context.queryClient.ensureQueryData(productsQueryOptions()),
  component: ProductsPage,
})

function ProductsPage() {
  // Data is already cached from loader
  const { data: products } = useSuspenseQuery(productsQueryOptions())

  return (
    <ul>
      {products.map((p) => (
        <li key={p.id}>{p.name} - ${p.price}</li>
      ))}
    </ul>
  )
}
```

### Mutations

```tsx
import { useMutation, useQueryClient } from '@tanstack/react-query'

function CreateProductForm() {
  const queryClient = useQueryClient()

  const createMutation = useMutation({
    mutationFn: async (data: { name: string; price: number }) => {
      const res = await fetch('/api/products', {
        method: 'POST',
        body: JSON.stringify(data),
      })
      return res.json()
    },
    onSuccess: () => {
      // Invalidate to refetch the list
      queryClient.invalidateQueries({ queryKey: ['products'] })
    },
  })

  return (
    <form onSubmit={(e) => {
      e.preventDefault()
      createMutation.mutate({ name: 'New Product', price: 99 })
    }}>
      <Button disabled={createMutation.isPending}>
        {createMutation.isPending ? 'Creating...' : 'Create'}
      </Button>
    </form>
  )
}
```

## Server Functions (createServerFn)

Server functions run on the server but can be called from client code.

> **⚠️ API Change:** The method `.validator()` was renamed to `.inputValidator()` in recent versions.
> If you see errors about `.validator()` not existing, change it to `.inputValidator()`.
> Also note: server functions with input validation should use `method: 'POST'` (not `'GET'`).

### Basic Server Function

```tsx
import { createServerFn } from '@tanstack/react-start'

export const getServerTime = createServerFn().handler(async () => {
  return new Date().toISOString()
})

// Call it anywhere
const time = await getServerTime()
```

### With Input Validation

```tsx
export const createUser = createServerFn({ method: 'POST' })
  .inputValidator((data: { name: string; email: string }) => data)
  .handler(async ({ data }) => {
    // data is typed and validated
    const user = await db.users.create(data)
    return user
  })

// Usage
await createUser({ data: { name: 'John', email: 'john@example.com' } })
```

### With Middleware

```tsx
import { createServerFn, createMiddleware } from '@tanstack/react-start'

const authMiddleware = createMiddleware().server(async ({ next, context }) => {
  const user = await getSessionUser()
  if (!user) throw new Error('Unauthorized')
  return next({ context: { user } })
})

export const getMyProfile = createServerFn()
  .middleware([authMiddleware])
  .handler(async ({ context }) => {
    // context.user is available from middleware
    return context.user
  })
```

## API Routes

Create REST endpoints in `src/routes/api/`:

```tsx
// src/routes/api/products.ts
import { createFileRoute } from '@tanstack/react-router'
import { createMiddleware } from '@tanstack/react-start'

const loggingMiddleware = createMiddleware().server(async ({ next }) => {
  console.info('API Request:', new Date().toISOString())
  return next()
})

export const Route = createFileRoute('/api/products')({
  server: {
    middleware: [loggingMiddleware],
    handlers: {
      GET: async ({ request }) => {
        const products = await db.products.findMany()
        return Response.json(products)
      },
      POST: async ({ request }) => {
        const body = await request.json()
        const product = await db.products.create(body)
        return Response.json(product, { status: 201 })
      },
    },
  },
})
```

### Dynamic API Routes

```tsx
// src/routes/api/products.$id.ts
export const Route = createFileRoute('/api/products/$id')({
  server: {
    handlers: {
      GET: async ({ request, params }) => {
        const product = await db.products.findById(params.id)
        if (!product) {
          return new Response('Not found', { status: 404 })
        }
        return Response.json(product)
      },
      DELETE: async ({ request, params }) => {
        await db.products.delete(params.id)
        return new Response(null, { status: 204 })
      },
    },
  },
})
```

## Deferred Data Loading

Load critical data immediately, defer non-critical data:

```tsx
import { Await, createFileRoute } from '@tanstack/react-router'
import { Suspense } from 'react'
import { Skeleton } from '@/components/ui/skeleton'

export const Route = createFileRoute('/dashboard')({
  loader: async () => ({
    // Await critical data
    user: await fetchUser(),
    // Defer non-critical data (returns Promise)
    analytics: fetchAnalytics(), // Note: no await
    recommendations: fetchRecommendations(),
  }),
  component: Dashboard,
})

function Dashboard() {
  const { user, analytics, recommendations } = Route.useLoaderData()

  return (
    <div>
      {/* Renders immediately */}
      <h1>Welcome, {user.name}</h1>

      {/* Streams in when ready */}
      <Suspense fallback={<Skeleton className="h-32" />}>
        <Await promise={analytics}>
          {(data) => <AnalyticsChart data={data} />}
        </Await>
      </Suspense>

      <Suspense fallback={<Skeleton className="h-48" />}>
        <Await promise={recommendations}>
          {(items) => <RecommendationsList items={items} />}
        </Await>
      </Suspense>
    </div>
  )
}
```

## Adding shadcn/ui Components

### Install New Components

```bash
# Single component
npx shadcn@latest add button

# Multiple components
npx shadcn@latest add card dialog dropdown-menu

# Browse all at https://ui.shadcn.com/docs/components
```

### Using Components

```tsx
import { Button } from '@/components/ui/button'
import { Card, CardContent, CardHeader, CardTitle } from '@/components/ui/card'
import {
  Dialog,
  DialogContent,
  DialogHeader,
  DialogTitle,
  DialogTrigger,
} from '@/components/ui/dialog'

function MyComponent() {
  return (
    <Card>
      <CardHeader>
        <CardTitle>My Card</CardTitle>
      </CardHeader>
      <CardContent>
        <Dialog>
          <DialogTrigger asChild>
            <Button>Open Dialog</Button>
          </DialogTrigger>
          <DialogContent>
            <DialogHeader>
              <DialogTitle>Dialog Title</DialogTitle>
            </DialogHeader>
            <p>Dialog content here</p>
          </DialogContent>
        </Dialog>
      </CardContent>
    </Card>
  )
}
```

### The cn() Utility

Merge Tailwind classes conditionally:

```tsx
import { cn } from '@/lib/utils'

function MyButton({ className, variant }: { className?: string; variant?: 'primary' | 'secondary' }) {
  return (
    <button
      className={cn(
        'px-4 py-2 rounded-sm font-medium',
        variant === 'primary' && 'bg-primary text-primary-foreground',
        variant === 'secondary' && 'bg-secondary text-secondary-foreground',
        className
      )}
    />
  )
}
```

## SEO and Meta Tags

### Per-Route Head Configuration

```tsx
// src/routes/blog.$slug.tsx
import { createFileRoute } from '@tanstack/react-router'
import { seo } from '~/utils/seo'

export const Route = createFileRoute('/blog/$slug')({
  loader: async ({ params }) => {
    const post = await fetchPost(params.slug)
    return { post }
  },
  head: ({ loaderData }) => ({
    meta: [
      ...seo({
        title: loaderData.post.title,
        description: loaderData.post.excerpt,
        image: loaderData.post.coverImage,
      }),
    ],
  }),
  component: BlogPost,
})
```

### The seo() Helper

```tsx
// src/utils/seo.ts
export const seo = ({
  title,
  description,
  keywords,
  image,
}: {
  title: string
  description?: string
  image?: string
  keywords?: string
}) => {
  return [
    { title },
    { name: 'description', content: description },
    { name: 'keywords', content: keywords },
    { name: 'twitter:title', content: title },
    { name: 'twitter:description', content: description },
    { name: 'og:title', content: title },
    { name: 'og:description', content: description },
    ...(image ? [
      { name: 'twitter:image', content: image },
      { name: 'og:image', content: image },
    ] : []),
  ]
}
```

## Error Handling

### Route-Level Error Boundary

```tsx
import { createFileRoute, ErrorComponent } from '@tanstack/react-router'

export const Route = createFileRoute('/posts/$postId')({
  loader: async ({ params }) => {
    const post = await fetchPost(params.postId)
    if (!post) throw notFound()
    return { post }
  },
  errorComponent: ({ error, reset }) => (
    <div className="p-4">
      <h2 className="text-destructive">Error loading post</h2>
      <p>{error.message}</p>
      <Button onClick={reset}>Try Again</Button>
    </div>
  ),
  notFoundComponent: () => (
    <div className="p-4">
      <h2>Post not found</h2>
      <Link to="/posts">Back to posts</Link>
    </div>
  ),
  component: PostPage,
})
```

### Server Function Errors

```tsx
import { notFound } from '@tanstack/react-router'
import { createServerFn } from '@tanstack/react-start'

export const fetchPost = createServerFn({ method: 'POST' })
  .inputValidator((id: string) => id)
  .handler(async ({ data: id }) => {
    const res = await fetch(`/api/posts/${id}`)

    if (res.status === 404) {
      throw notFound() // Triggers notFoundComponent
    }

    if (!res.ok) {
      throw new Error('Failed to fetch post') // Triggers errorComponent
    }

    return res.json()
  })
```

## Navigation

### Link Component

```tsx
import { Link } from '@tanstack/react-router'

// Basic link
<Link to="/about">About</Link>

// With params
<Link to="/posts/$postId" params={{ postId: '123' }}>
  View Post
</Link>

// With search params
<Link to="/products" search={{ category: 'electronics', page: 1 }}>
  Electronics
</Link>

// Active styling
<Link
  to="/dashboard"
  activeProps={{ className: 'font-bold text-primary' }}
  inactiveProps={{ className: 'text-muted-foreground' }}
>
  Dashboard
</Link>
```

### Programmatic Navigation

```tsx
import { useNavigate, useRouter } from '@tanstack/react-router'

function MyComponent() {
  const navigate = useNavigate()
  const router = useRouter()

  const handleClick = () => {
    // Navigate to a route
    navigate({ to: '/posts/$postId', params: { postId: '123' } })

    // Or with the router
    router.navigate({ to: '/dashboard' })
  }

  const handleBack = () => {
    router.history.back()
  }
}
```

## Search Parameters

### Type-Safe Search Params

```tsx
import { createFileRoute } from '@tanstack/react-router'
import { z } from 'zod'

const searchSchema = z.object({
  page: z.number().default(1),
  search: z.string().optional(),
  sort: z.enum(['asc', 'desc']).default('desc'),
})

export const Route = createFileRoute('/products')({
  validateSearch: searchSchema,
  component: ProductsPage,
})

function ProductsPage() {
  const { page, search, sort } = Route.useSearch()
  const navigate = useNavigate()

  const setPage = (newPage: number) => {
    navigate({
      search: (prev) => ({ ...prev, page: newPage }),
    })
  }

  return (
    <div>
      <p>Page: {page}, Sort: {sort}</p>
      <Button onClick={() => setPage(page + 1)}>Next Page</Button>
    </div>
  )
}
```

## Middleware Patterns

### Logging Middleware

```tsx
// src/utils/loggingMiddleware.tsx
import { createMiddleware } from '@tanstack/react-start'

export const logMiddleware = createMiddleware({ type: 'function' })
  .client(async (ctx) => {
    const startTime = Date.now()
    const result = await ctx.next()
    console.log(`Request took ${Date.now() - startTime}ms`)
    return result
  })
  .server(async (ctx) => {
    console.log('Server processing:', ctx.data)
    return ctx.next()
  })
```

### Auth Middleware

```tsx
import { createMiddleware } from '@tanstack/react-start'
import { getRequestHeaders } from '@tanstack/react-start/server'

export const authMiddleware = createMiddleware().server(async ({ next }) => {
  const headers = getRequestHeaders()
  const token = headers.get('authorization')?.replace('Bearer ', '')

  if (!token) {
    throw new Response('Unauthorized', { status: 401 })
  }

  const user = await verifyToken(token)
  return next({ context: { user } })
})
```

## Theming

### CSS Variables (src/styles/app.css)

```css
@import 'tailwindcss';

@theme {
  /* Customize your theme here */
  --color-primary: oklch(0.7 0.15 250);
  --color-primary-foreground: oklch(0.98 0 0);
}

/* Dark mode is enabled by default via class="dark" on <html> */
```

### Toggle Dark Mode

```tsx
function ThemeToggle() {
  const [isDark, setIsDark] = useState(true)

  const toggle = () => {
    document.documentElement.classList.toggle('dark')
    setIsDark(!isDark)
  }

  return (
    <Button variant="ghost" onClick={toggle}>
      {isDark ? <Sun /> : <Moon />}
    </Button>
  )
}
```

## Updating the Sidebar

Edit `src/components/app-sidebar.tsx`:

```tsx
const navItems = [
  { title: 'Home', url: '/', icon: Home },
  { title: 'Products', url: '/products', icon: ShoppingBag },
  { title: 'Settings', url: '/settings', icon: Settings },
  // Add more items...
]
```

## Common Patterns

### Loading States with Skeleton

```tsx
import { Skeleton } from '@/components/ui/skeleton'

function LoadingCard() {
  return (
    <Card>
      <CardHeader>
        <Skeleton className="h-6 w-48" />
      </CardHeader>
      <CardContent>
        <Skeleton className="h-4 w-full mb-2" />
        <Skeleton className="h-4 w-3/4" />
      </CardContent>
    </Card>
  )
}
```

### Form with Server Action

```tsx
import { createServerFn } from '@tanstack/react-start'
import { useForm } from '@tanstack/react-form'

const submitForm = createServerFn({ method: 'POST' })
  .inputValidator((data: { email: string }) => data)
  .handler(async ({ data }) => {
    await subscribeToNewsletter(data.email)
    return { success: true }
  })

function NewsletterForm() {
  const form = useForm({
    defaultValues: { email: '' },
    onSubmit: async ({ value }) => {
      await submitForm({ data: value })
    },
  })

  return (
    <form onSubmit={(e) => { e.preventDefault(); form.handleSubmit() }}>
      <form.Field name="email">
        {(field) => (
          <Input
            type="email"
            value={field.state.value}
            onChange={(e) => field.handleChange(e.target.value)}
          />
        )}
      </form.Field>
      <Button type="submit">Subscribe</Button>
    </form>
  )
}
```

## Resources

- [TanStack Start Docs](https://tanstack.com/start/latest/docs)
- [TanStack Router Docs](https://tanstack.com/router/latest/docs)
- [TanStack Query Docs](https://tanstack.com/query/latest/docs)
- [shadcn/ui Components](https://ui.shadcn.com/docs/components)
- [Tailwind CSS v4 Docs](https://tailwindcss.com/docs)
- [Lucide Icons](https://lucide.dev/icons)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/kadajett) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
