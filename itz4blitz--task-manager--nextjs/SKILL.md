---
name: nextjs
description: Next.js 16 App Router patterns and best practices for this project Use when this capability is needed.
metadata:
  author: itz4blitz
---

# Next.js 16 Skill

## Overview

This project uses **Next.js 16.1.4** with the App Router, TypeScript, and Tailwind CSS. Next.js 16 brings significant performance improvements with Turbopack as the default bundler and built-in React Compiler support.

## Project Configuration

**Detected Version**: Next.js 16.1.4
**Router**: App Router (default)
**Language**: TypeScript 5.x
**Styling**: Tailwind CSS 4.x
**Node.js**: 20.9+ required

**Key Files**:
- `next.config.ts` - Next.js configuration (TypeScript)
- `src/app/layout.tsx` - Root layout with metadata
- `src/app/page.tsx` - Home page
- `src/app/globals.css` - Global styles
- `tsconfig.json` - TypeScript configuration with Next.js plugin

## App Router Fundamentals

### File-Based Routing

```
src/app/
├── layout.tsx          # Root layout (required)
├── page.tsx            # Home route (/)
├── globals.css         # Global styles
├── favicon.ico         # Site favicon
└── tasks/              # Example nested route
    └── page.tsx        # /tasks route
```

### Special Files

| File | Purpose | Required |
|------|---------|----------|
| `layout.tsx` | Shared UI for route segment | Yes (root) |
| `page.tsx` | Route UI, makes route publicly accessible | Yes |
| `loading.tsx` | Loading UI with Suspense | No |
| `error.tsx` | Error UI boundary | No |
| `not-found.tsx` | 404 UI | No |

## Server vs Client Components

### Server Components (Default)

By default, all components in the App Router are Server Components:

```tsx
// src/app/page.tsx (Server Component by default)
export default function Home() {
  // Runs on server only
  // Can directly access backend resources
  // Cannot use hooks or event handlers
  return <div>Server-rendered content</div>;
}
```

**Benefits**:
- Zero JavaScript sent to client
- Direct access to backend resources
- Better SEO
- Faster initial page load

### Client Components

Use `'use client'` directive when you need:
- React hooks (`useState`, `useEffect`, etc.)
- Event handlers (`onClick`, `onChange`, etc.)
- Browser APIs
- Interactive UI

```tsx
'use client'; // Must be at the top of the file

import { useState } from 'react';

export default function TaskInput() {
  const [value, setValue] = useState('');

  return (
    <input
      type="text"
      value={value}
      onChange={(e) => setValue(e.target.value)}
    />
  );
}
```

### Component Boundary Best Practice

```tsx
// src/app/page.tsx (Server Component)
import TaskList from '@/components/TaskList'; // Client component

export default function Home() {
  // Server logic here
  return (
    <main>
      <h1>Task Manager</h1>
      <TaskList /> {/* Client interactivity isolated here */}
    </main>
  );
}
```

**Rule**: Keep Server Components as far up the tree as possible, only use Client Components where interactivity is needed.

## Metadata API

```tsx
// src/app/layout.tsx
import type { Metadata } from 'next';

export const metadata: Metadata = {
  title: 'Task Manager',
  description: 'A simple task management app',
};

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

**Dynamic Metadata**:
```tsx
// src/app/tasks/[id]/page.tsx
export async function generateMetadata({ params }: Props): Promise<Metadata> {
  const task = await getTask(params.id);
  return { title: task.description };
}
```

## Common Patterns (2026)

### 1. Project Structure

```
src/
├── app/                    # App Router pages
│   ├── layout.tsx
│   ├── page.tsx
│   └── globals.css
├── components/             # React components
│   ├── ui/                # Generic UI components
│   ├── layout/            # Layout components
│   └── features/          # Feature-specific components
└── lib/                   # Utilities and types
    ├── types.ts
    └── utils.ts
```

**Best Practice**: Use `@/` import alias (configured in `tsconfig.json`):
```tsx
import { Task } from '@/lib/types';
import TaskInput from '@/components/TaskInput';
```

### 2. Font Optimization

Next.js 16 includes automatic font optimization with `next/font`:

```tsx
// src/app/layout.tsx
import { Geist, Geist_Mono } from 'next/font/google';

const geistSans = Geist({
  variable: '--font-geist-sans',
  subsets: ['latin'],
});

const geistMono = Geist_Mono({
  variable: '--font-geist-mono',
  subsets: ['latin'],
});

export default function RootLayout({ children }) {
  return (
    <html lang="en">
      <body className={`${geistSans.variable} ${geistMono.variable}`}>
        {children}
      </body>
    </html>
  );
}
```

### 3. Image Optimization

```tsx
import Image from 'next/image';

<Image
  src="/task-icon.png"
  alt="Task icon"
  width={24}
  height={24}
  priority // For above-the-fold images
/>
```

### 4. CSS and Tailwind

```tsx
// Global styles in src/app/globals.css
@tailwind base;
@tailwind components;
@tailwind utilities;

// Component-level Tailwind classes
<div className="flex min-h-screen items-center justify-center bg-zinc-50">
  <main className="max-w-3xl w-full px-4">
    {/* Content */}
  </main>
</div>
```

## Performance Optimizations (Next.js 16)

### 1. Turbopack (Default)

Turbopack is now the default bundler in Next.js 16:
- **2-5× faster** production builds
- **Up to 10× faster** Fast Refresh
- **No configuration needed** - works out of the box

Enable filesystem caching for even faster rebuilds:
```ts
// next.config.ts
export default {
  experimental: {
    turbopackFileSystemCacheForDev: true,
  },
};
```

### 2. React Compiler (Built-in)

React Compiler is stable in Next.js 16:
- **Automatic memoization** - no need for manual `useMemo`, `useCallback`
- **Reduces re-renders** automatically
- **Enabled by default** for new projects

```tsx
// No need for useMemo anymore - React Compiler handles it
const filteredTasks = tasks.filter(task => {
  if (filter === 'active') return !task.completed;
  if (filter === 'completed') return task.completed;
  return true;
});
```

### 3. Async Request APIs (Stable)

```tsx
// Async components (Server Components)
export default async function TasksPage() {
  const tasks = await fetchTasks(); // Direct async/await
  return <TaskList tasks={tasks} />;
}
```

## Best Practices from Research (2026)

### 1. Separation of Concerns

- **Server Components**: Data fetching, backend logic
- **Client Components**: User interactions, state management
- **Shared Components**: UI components used in both

### 2. TypeScript First

- Always use TypeScript for better type safety
- Use the TypeScript plugin for advanced type-checking
- Leverage TypeScript 5.x features

### 3. Production Checklist

Before deploying:
- ✅ Run `npm run build` to check for build errors
- ✅ Enable TypeScript strict mode (`strict: true` in tsconfig.json)
- ✅ Test responsive design (mobile-first)
- ✅ Verify accessibility (keyboard navigation, ARIA)
- ✅ Check bundle size with `npm run build`

### 4. Development Workflow

```bash
# Development server (Turbopack)
npm run dev

# Production build
npm run build

# Start production server
npm run start

# Linting
npm run lint
```

## Common Pitfalls (2026)

### ❌ Don't: Mix Server and Client concerns

```tsx
'use client';

export default function BadComponent() {
  const data = await fetch('/api/tasks'); // ❌ async/await in Client Component
  return <div>{data}</div>;
}
```

### ✅ Do: Separate concerns properly

```tsx
// Server Component
async function TasksPage() {
  const tasks = await fetchTasks(); // ✅ Server-side data fetching
  return <TaskList tasks={tasks} />; // Pass to Client Component
}

// Client Component
'use client';
function TaskList({ tasks }: { tasks: Task[] }) {
  const [filter, setFilter] = useState('all'); // ✅ Client-side state
  // ...
}
```

### ❌ Don't: Use 'use client' everywhere

```tsx
'use client'; // ❌ Unnecessary if no interactivity

export default function StaticContent() {
  return <div>Just static content</div>;
}
```

### ✅ Do: Default to Server Components

```tsx
// No 'use client' needed - Server Component by default
export default function StaticContent() {
  return <div>Static content</div>; // ✅ Zero JS sent to client
}
```

### ❌ Don't: Import Client Components in Server Components carelessly

```tsx
// Server Component
import ClientComponent from './ClientComponent'; // Client Component

export default function Page() {
  // ❌ Passing non-serializable data to Client Component
  return <ClientComponent callback={() => console.log('test')} />;
}
```

### ✅ Do: Only pass serializable props

```tsx
// Server Component
export default function Page() {
  // ✅ Pass serializable data only
  return <ClientComponent data={{ id: 1, name: 'Task' }} />;
}
```

## References

- [Next.js 16 Official Docs](https://nextjs.org/docs)
- [Next.js 16 Blog Post](https://nextjs.org/blog/next-16)
- [App Router Guide](https://nextjs.org/docs/app)
- [Server Components](https://nextjs.org/docs/app/building-your-application/rendering/server-components)
- [Client Components](https://nextjs.org/docs/app/building-your-application/rendering/client-components)
- [TypeScript Configuration](https://nextjs.org/docs/app/api-reference/config/typescript)
- [Production Checklist](https://nextjs.org/docs/app/guides/production-checklist)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/itz4blitz) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
