---
name: tanstack-router
description: Bootstrap React projects with TanStack Router for type-safe file-based routing. Use when creating React SPAs, modern web applications with routing, or when the user wants type-safe navigation in React. Use when this capability is needed.
metadata:
  author: kadajett
---

# TanStack Router Bootstrapper

Creates modern React applications with TanStack Router v1 for fully type-safe, file-based routing.

## Prerequisites

```bash
# Check Node.js (v18+ recommended)
node --version && npm --version
```

## Create Project with Official CLI

### Using create-tanstack-app (Recommended)

```bash
# Interactive setup
npx create-tanstack-app@latest

# Or with bun
bunx create-tanstack-app@latest
```

### Manual Setup with Vite

If you prefer manual setup or need more control:

```bash
# Create Vite React project
npm create vite@latest PROJECT_NAME -- --template react-ts
cd PROJECT_NAME

# Install TanStack Router
npm install @tanstack/react-router @tanstack/router-devtools

# Install Vite plugin for file-based routing
npm install -D @tanstack/router-plugin
```

## Project Structure

```
my-tanstack-app/
├── src/
│   ├── routes/
│   │   ├── __root.tsx       # Root layout
│   │   ├── index.tsx        # Home page (/)
│   │   ├── about.tsx        # About page (/about)
│   │   └── posts/
│   │       ├── index.tsx    # Posts list (/posts)
│   │       └── $postId.tsx  # Dynamic route (/posts/:postId)
│   ├── routeTree.gen.ts     # Auto-generated route tree
│   ├── main.tsx             # App entry point
│   └── App.tsx
├── package.json
├── tsconfig.json
├── vite.config.ts
└── .gitignore
```

## Vite Configuration

Update `vite.config.ts`:

```typescript
import { defineConfig } from 'vite'
import react from '@vitejs/plugin-react'
import { TanStackRouterVite } from '@tanstack/router-plugin/vite'

export default defineConfig({
  plugins: [
    TanStackRouterVite(),
    react(),
  ],
})
```

## Route Configuration

### Root Route (__root.tsx)

```typescript
import { createRootRoute, Link, Outlet } from '@tanstack/react-router'
import { TanStackRouterDevtools } from '@tanstack/router-devtools'

export const Route = createRootRoute({
  component: () => (
    <>
      <nav className="flex gap-4 p-4 border-b">
        <Link to="/" className="[&.active]:font-bold">
          Home
        </Link>
        <Link to="/about" className="[&.active]:font-bold">
          About
        </Link>
        <Link to="/posts" className="[&.active]:font-bold">
          Posts
        </Link>
      </nav>
      <main className="p-4">
        <Outlet />
      </main>
      <TanStackRouterDevtools />
    </>
  ),
})
```

### Index Route (index.tsx)

```typescript
import { createFileRoute } from '@tanstack/react-router'

export const Route = createFileRoute('/')({
  component: Index,
})

function Index() {
  return (
    <div>
      <h1>Welcome Home</h1>
    </div>
  )
}
```

### Dynamic Route ($postId.tsx)

```typescript
import { createFileRoute } from '@tanstack/react-router'

export const Route = createFileRoute('/posts/$postId')({
  loader: async ({ params }) => {
    // Fetch post data
    const post = await fetchPost(params.postId)
    return { post }
  },
  component: PostPage,
})

function PostPage() {
  const { post } = Route.useLoaderData()
  return (
    <div>
      <h1>{post.title}</h1>
      <p>{post.content}</p>
    </div>
  )
}
```

## App Entry Point (main.tsx)

```typescript
import React from 'react'
import ReactDOM from 'react-dom/client'
import { RouterProvider, createRouter } from '@tanstack/react-router'
import { routeTree } from './routeTree.gen'

// Create the router instance
const router = createRouter({ routeTree })

// Register the router for type safety
declare module '@tanstack/react-router' {
  interface Register {
    router: typeof router
  }
}

ReactDOM.createRoot(document.getElementById('root')!).render(
  <React.StrictMode>
    <RouterProvider router={router} />
  </React.StrictMode>,
)
```

## Key Features to Implement

### Search Parameters (Type-safe)

```typescript
import { createFileRoute } from '@tanstack/react-router'
import { z } from 'zod'

const searchSchema = z.object({
  page: z.number().default(1),
  filter: z.string().optional(),
})

export const Route = createFileRoute('/posts/')({
  validateSearch: searchSchema,
  component: PostsList,
})

function PostsList() {
  const { page, filter } = Route.useSearch()
  // page and filter are fully typed!
}
```

### Data Loading

```typescript
export const Route = createFileRoute('/posts/$postId')({
  loader: async ({ params, context }) => {
    const post = await fetchPost(params.postId)
    return { post }
  },
  pendingComponent: () => <div>Loading...</div>,
  errorComponent: ({ error }) => <div>Error: {error.message}</div>,
})
```

### Route Context

```typescript
// In main.tsx
const router = createRouter({
  routeTree,
  context: {
    auth: undefined!, // Will be provided
  },
})

// In a route
export const Route = createFileRoute('/dashboard')({
  beforeLoad: ({ context }) => {
    if (!context.auth.isAuthenticated) {
      throw redirect({ to: '/login' })
    }
  },
})
```

## Recommended Dependencies

```bash
# State management
npm install @tanstack/react-query

# Form handling
npm install @tanstack/react-form

# Validation
npm install zod

# Styling (pick one)
npm install tailwindcss postcss autoprefixer  # Tailwind
npm install @emotion/react @emotion/styled    # Emotion
```

## TypeScript Configuration

Update `tsconfig.json`:

```json
{
  "compilerOptions": {
    "target": "ES2020",
    "useDefineForClassFields": true,
    "lib": ["ES2020", "DOM", "DOM.Iterable"],
    "module": "ESNext",
    "skipLibCheck": true,
    "moduleResolution": "bundler",
    "allowImportingTsExtensions": true,
    "resolveJsonModule": true,
    "isolatedModules": true,
    "noEmit": true,
    "jsx": "react-jsx",
    "strict": true,
    "noUnusedLocals": true,
    "noUnusedParameters": true,
    "noFallthroughCasesInSwitch": true,
    "paths": {
      "@/*": ["./src/*"]
    }
  },
  "include": ["src"],
  "references": [{ "path": "./tsconfig.node.json" }]
}
```

## Development Commands

```bash
# Start dev server
npm run dev

# Build for production
npm run build

# Preview production build
npm run preview

# Type check
npx tsc --noEmit

# Lint
npm run lint
```

## GitHub Actions CI

Create `.github/workflows/ci.yml`:

```yaml
name: CI

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Setup Node
        uses: actions/setup-node@v4
        with:
          node-version: 20
          cache: 'npm'

      - name: Install dependencies
        run: npm ci

      - name: Type check
        run: npx tsc --noEmit

      - name: Lint
        run: npm run lint

      - name: Build
        run: npm run build
```

## Post-Setup Checklist

1. [ ] Configure route tree generation
2. [ ] Set up authentication context if needed
3. [ ] Add error boundaries
4. [ ] Configure meta tags (title, description)
5. [ ] Set up TanStack Query for data fetching
6. [ ] Add loading states and skeletons
7. [ ] Configure Tailwind CSS or styling solution

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/kadajett) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
