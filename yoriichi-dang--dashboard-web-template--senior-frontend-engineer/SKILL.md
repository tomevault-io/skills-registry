---
name: senior-frontend-engineer
description: Production-grade frontend development for senior engineers building large-scale React 19.2 + Next.js 16 applications. Covers architecture, performance, testing (Jest, Playwright), accessibility (WCAG 2.1), state management (React Query, Zustand), styling (Tailwind, shadcn/ui), and enterprise patterns. Use when building production applications, reviewing code, optimizing performance, implementing accessibility, writing tests, or following senior-level best practices for modern React/Next.js development. Use when this capability is needed.
metadata:
  author: yoriichi-dang
---

# Senior Frontend Engineer Skill

> **Enterprise-Grade React 19.2 + Next.js 16 Development**

**Version:** 2025.12  
**Stack:** React 19.2 · Next.js 16 · TypeScript · Tailwind CSS · shadcn/ui · React Query · Zod · Jest · Playwright · Framer Motion

---

## 🎯 Core Philosophy

This skill embodies senior-level frontend engineering principles used in production at companies like Vercel, Airbnb, and Stripe:

### Five Pillars of Excellence

1. **Server-First Architecture** (RSC-First)
   - Default to Server Components
   - 'use client' only when necessary (state, effects, browser APIs)
   - Minimize client-side JavaScript bundle

2. **Type Safety Everywhere**
   - Strict TypeScript with no `any`
   - Zod schemas at API boundaries
   - End-to-end type safety (DB → API → UI)

3. **Performance by Default**
   - Core Web Vitals: LCP < 2.5s, FID < 100ms, CLS < 0.1
   - Code splitting and lazy loading
   - Aggressive caching strategies
   - Bundle size budgets enforced

4. **Accessibility as Foundation**
   - WCAG 2.1 Level AA compliance
   - Semantic HTML first
   - Keyboard navigation complete
   - Screen reader tested

5. **Testing at Scale**
   - 70% unit tests (pure functions, hooks)
   - 20% integration tests (component flows)
   - 10% E2E tests (critical paths)
   - Visual regression for UI components

---

## 📚 Reference Documentation

This skill includes comprehensive reference guides:

| Document | Purpose | When to Read |
|----------|---------|--------------|
| **01-react-hooks.md** | All React 19.2 hooks with examples | When implementing state, effects, forms |
| **02-react-components.md** | Components & APIs reference | When using Activity, Suspense, memo |
| **03-nextjs.md** | Next.js 16 architecture & patterns | When setting up routes, caching, SSR/SSG |
| **06-patterns.md** | Design patterns & anti-patterns | When designing components, avoiding mistakes |
| **07-testing.md** | Jest, RTL, Playwright guide | When writing tests, setting up CI |
| **08-performance.md** | Optimization techniques | When optimizing bundle, images, rendering |
| **09-accessibility.md** | WCAG compliance guide | When ensuring a11y, keyboard nav, ARIA |

**Usage:** Read relevant reference documents BEFORE implementing features. They contain battle-tested patterns from production systems.

---

## 🏗️ Project Architecture

### Recommended Structure

```
src/
├── app/                          # Next.js App Router
│   ├── (auth)/                   # Route group (no URL segment)
│   │   ├── login/
│   │   │   ├── page.tsx
│   │   │   ├── loading.tsx       # ⚠️ REQUIRED
│   │   │   └── error.tsx         # ⚠️ REQUIRED
│   │   └── register/page.tsx
│   ├── (dashboard)/
│   │   ├── layout.tsx            # Shared layout
│   │   ├── page.tsx
│   │   ├── posts/
│   │   └── settings/
│   ├── api/                      # Route handlers
│   │   ├── posts/route.ts
│   │   └── users/[id]/route.ts
│   ├── layout.tsx                # Root layout
│   ├── page.tsx                  # Homepage
│   ├── proxy.ts                  # Next 16: replaces middleware
│   ├── global-error.tsx          # Root error boundary
│   └── not-found.tsx
│
├── features/                     # Feature-based modules
│   └── [feature]/                # e.g., posts, auth, analytics
│       ├── components/           # Feature-specific components
│       │   ├── PostCard.tsx
│       │   └── PostCard.test.tsx
│       ├── hooks/                # Custom hooks
│       │   ├── usePostActions.ts
│       │   └── usePostActions.test.ts
│       ├── api/                  # API calls + server actions
│       │   ├── queries.ts        # React Query hooks
│       │   ├── mutations.ts
│       │   └── actions.ts        # Server Actions
│       ├── types/                # Zod schemas + TypeScript types
│       │   ├── schemas.ts
│       │   └── types.ts
│       ├── utils/                # Feature utilities
│       └── index.ts              # Public API (ONLY export from here)
│
├── shared/                       # Cross-feature code
│   ├── ui/                       # shadcn/ui components
│   │   ├── button.tsx
│   │   ├── card.tsx
│   │   ├── dialog.tsx
│   │   └── ...
│   ├── components/               # Shared components
│   │   ├── Header.tsx
│   │   ├── Footer.tsx
│   │   └── LoadingSpinner.tsx
│   ├── hooks/                    # Shared hooks
│   │   ├── useDebounce.ts
│   │   ├── useMediaQuery.ts
│   │   └── useLocalStorage.ts
│   ├── lib/                      # Utilities & config
│   │   ├── utils.ts              # cn() and helpers
│   │   ├── api-client.ts         # Axios/fetch wrapper
│   │   ├── query-client.ts       # React Query config
│   │   └── db.ts                 # Prisma client
│   ├── types/                    # Global types
│   │   └── common.ts
│   └── constants/
│       └── config.ts
│
├── test/                         # Test utilities
│   ├── setup.ts                  # Jest setup
│   ├── mocks/
│   │   ├── handlers.ts           # MSW handlers
│   │   └── server.ts             # MSW server
│   └── factories/                # Test data factories
│       ├── user.ts
│       └── post.ts
│
└── tests/                        # E2E tests
    └── e2e/
        ├── auth/
        │   └── login.spec.ts
        └── posts/
            └── create-post.spec.ts
```

### Import Rules

```tsx
// ✅ GOOD: Import from feature's public API
import { PostCard, usePostActions } from '@/features/posts';

// ❌ BAD: Deep imports (breaks encapsulation)
import { PostCard } from '@/features/posts/components/PostCard';

// ✅ GOOD: Barrel export in features/posts/index.ts
export { PostCard } from './components/PostCard';
export { usePostActions } from './hooks/usePostActions';
export type { Post, PostFilters } from './types/types';
```

---

## ⚡ Workflow Guide

### 1. Feature Implementation Workflow

When implementing a new feature, follow this systematic approach:

#### Phase 1: Planning & Types
1. **Read PRD/Requirements** - Understand the feature fully
2. **Design data model** - Define entities and relationships
3. **Create Zod schemas** (`features/[feature]/types/schemas.ts`)
4. **Generate TypeScript types** from schemas
5. **Plan component tree** - Identify Server vs Client Components

#### Phase 2: API Layer
1. **Read references/03-nextjs.md** for data fetching patterns
2. **Implement Server Actions** for mutations (`features/[feature]/api/actions.ts`)
3. **Create React Query hooks** (`features/[feature]/api/queries.ts`)
4. **Add MSW handlers** for testing (`test/mocks/handlers.ts`)

#### Phase 3: UI Implementation
1. **Read references/01-react-hooks.md** before using hooks
2. **Start with Server Components** (default)
3. **Add 'use client'** only where needed (state, effects, browser APIs)
4. **Use shadcn/ui components** from `shared/ui/`
5. **Follow references/06-patterns.md** for component structure

#### Phase 4: Testing
1. **Read references/07-testing.md** for testing strategy
2. **Write unit tests** for pure functions and hooks
3. **Write integration tests** for component interactions
4. **Write E2E tests** for critical user flows (Playwright)
5. **Add visual regression tests** if UI-heavy

#### Phase 5: Optimization
1. **Read references/08-performance.md** for optimization techniques
2. **Check bundle size** - ensure < 200KB for route
3. **Add code splitting** with `lazy()` for heavy components
4. **Optimize images** using `next/image`
5. **Profile with React Profiler** if performance issues

#### Phase 6: Accessibility
1. **Read references/09-accessibility.md** for a11y requirements
2. **Run axe DevTools** on components
3. **Test keyboard navigation** (Tab, Enter, Escape, Arrows)
4. **Test with screen reader** (NVDA, VoiceOver)
5. **Verify WCAG 2.1 AA** compliance

### 2. Code Review Workflow

When reviewing code, check these items:

#### Architecture
- [ ] Server Components by default, 'use client' only when needed
- [ ] Features properly encapsulated (imports from public API only)
- [ ] No circular dependencies between features
- [ ] loading.tsx and error.tsx present for routes

#### Type Safety
- [ ] No `any` types (except for truly dynamic data)
- [ ] Zod schemas validate all external data (API, forms, env)
- [ ] DTOs mapped to ViewModels at boundaries
- [ ] Props interfaces well-defined

#### Performance
- [ ] Bundle size checked (`ANALYZE=true npm run build`)
- [ ] Heavy components code-split with `lazy()`
- [ ] Images use `next/image` with proper sizing
- [ ] Expensive calculations memoized (if profiled)
- [ ] React Query staleTime configured appropriately

#### Accessibility
- [ ] Semantic HTML used (not div-soup)
- [ ] Interactive elements keyboard accessible
- [ ] ARIA attributes correct (or unnecessary)
- [ ] Color contrast meets WCAG AA (4.5:1)
- [ ] Alt text for images

#### Testing
- [ ] Unit tests for business logic
- [ ] Integration tests for user flows
- [ ] E2E tests for critical paths
- [ ] Tests use proper queries (getByRole, getByLabelText)
- [ ] Async properly handled (waitFor, findBy)

#### Code Quality
- [ ] Single Responsibility Principle followed
- [ ] No premature optimization
- [ ] Effects have proper cleanup
- [ ] Derived state not synced with effects
- [ ] Stable keys for lists (IDs, not indexes)

---

## 🔑 Key Patterns & Best Practices

### React 19.2 New Features

#### useEffectEvent (Avoid Stale Closures)

```tsx
'use client';

import { useEffect } from 'react';
import { useEffectEvent } from 'react';

function Chat({ roomId, onMessage }: Props) {
  const [theme, setTheme] = useState('dark');

  // ✅ GOOD: onMessage always has latest theme
  const handleMessage = useEffectEvent((msg: string) => {
    showNotification(msg, { theme }); // Always current theme
  });

  useEffect(() => {
    const connection = createConnection(roomId);
    connection.on('message', handleMessage);
    
    return () => connection.disconnect();
  }, [roomId]); // handleMessage NOT in deps!

  // ...
}
```

#### useActionState (Form Handling)

```tsx
'use client';

import { useActionState } from 'react';
import { createPost } from '@/features/posts/api/actions';

function CreatePostForm() {
  const [state, formAction, isPending] = useActionState(createPost, {
    error: null,
    success: false,
  });

  return (
    <form action={formAction}>
      <input name="title" required />
      <textarea name="content" required />
      
      {state.error && (
        <p role="alert" className="text-red-500">{state.error}</p>
      )}
      
      <button disabled={isPending}>
        {isPending ? 'Creating...' : 'Create Post'}
      </button>
    </form>
  );
}
```

#### Activity Component (Preserve State)

```tsx
'use client';

import { Activity } from 'react';

function TabPanel({ activeTab }: Props) {
  return (
    <>
      <Activity mode={activeTab === 'posts' ? 'visible' : 'hidden'}>
        <PostsTab /> {/* State preserved when hidden */}
      </Activity>
      
      <Activity mode={activeTab === 'comments' ? 'visible' : 'hidden'}>
        <CommentsTab /> {/* State preserved when hidden */}
      </Activity>
    </>
  );
}
```

### Next.js 16 Features

#### Cache Profiles

```tsx
'use server';

import { cacheLife, cacheTag } from 'next/cache';

// Short-lived (seconds)
export async function getStockPrice() {
  'use cache';
  cacheLife('seconds');
  return await fetchStockAPI();
}

// Medium-lived (hours)
export async function getBlogPosts() {
  'use cache';
  cacheLife('hours');
  cacheTag('blog-posts');
  return await db.post.findMany();
}

// Long-lived (days)
export async function getStaticPage() {
  'use cache';
  cacheLife('days');
  return await db.page.findUnique({ where: { slug } });
}
```

#### Proxy (Replaces Middleware)

```tsx
// app/proxy.ts
import type { ProxyRequest, ProxyResponse } from 'next/server';

export function proxy(request: ProxyRequest): ProxyResponse {
  const { pathname } = request.nextUrl;

  // 1. Authentication
  if (pathname.startsWith('/dashboard')) {
    const token = request.cookies.get('auth-token');
    if (!token) {
      return Response.redirect(new URL('/login', request.url));
    }
  }

  // 2. API Rewrites
  if (pathname.startsWith('/api/v1')) {
    return request.rewrite(new URL(pathname, process.env.API_URL));
  }

  // 3. Custom headers
  const response = request.next();
  response.headers.set('x-custom-header', 'value');
  
  return response;
}

export const config = {
  matcher: ['/dashboard/:path*', '/api/:path*'],
};
```

### Type Safety with Zod

```tsx
// features/posts/types/schemas.ts
import { z } from 'zod';

// API Response (DTO)
export const PostDTOSchema = z.object({
  id: z.string().uuid(),
  title: z.string(),
  content: z.string(),
  author_name: z.string(),
  created_at: z.string().datetime(),
  is_published: z.boolean(),
});

export type PostDTO = z.infer<typeof PostDTOSchema>;

// UI Model (ViewModel)
export interface Post {
  id: string;
  title: string;
  content: string;
  authorName: string;
  createdAt: Date;
  isPublished: boolean;
}

// Mapper
export function toPost(dto: PostDTO): Post {
  return {
    id: dto.id,
    title: dto.title,
    content: dto.content,
    authorName: dto.author_name,
    createdAt: new Date(dto.created_at),
    isPublished: dto.is_published,
  };
}

// API function with validation
export async function getPost(id: string): Promise<Post> {
  const response = await fetch(`/api/posts/${id}`);
  const data = await response.json();
  
  // Throws if invalid - caught by error boundary
  const validated = PostDTOSchema.parse(data);
  
  return toPost(validated);
}
```

---

## 🎨 Styling with Tailwind + shadcn/ui

### Component Pattern

```tsx
// shared/ui/button.tsx
import * as React from 'react';
import { Slot } from '@radix-ui/react-slot';
import { cva, type VariantProps } from 'class-variance-authority';
import { cn } from '@/shared/lib/utils';

const buttonVariants = cva(
  'inline-flex items-center justify-center rounded-md text-sm font-medium transition-colors focus-visible:outline-none focus-visible:ring-2 focus-visible:ring-offset-2 disabled:pointer-events-none disabled:opacity-50',
  {
    variants: {
      variant: {
        default: 'bg-primary text-primary-foreground hover:bg-primary/90',
        destructive: 'bg-destructive text-destructive-foreground hover:bg-destructive/90',
        outline: 'border border-input bg-background hover:bg-accent hover:text-accent-foreground',
        ghost: 'hover:bg-accent hover:text-accent-foreground',
      },
      size: {
        default: 'h-10 px-4 py-2',
        sm: 'h-9 px-3',
        lg: 'h-11 px-8',
        icon: 'h-10 w-10',
      },
    },
    defaultVariants: {
      variant: 'default',
      size: 'default',
    },
  }
);

export interface ButtonProps
  extends React.ButtonHTMLAttributes<HTMLButtonElement>,
    VariantProps<typeof buttonVariants> {
  asChild?: boolean;
}

export const Button = React.forwardRef<HTMLButtonElement, ButtonProps>(
  ({ className, variant, size, asChild = false, ...props }, ref) => {
    const Comp = asChild ? Slot : 'button';
    return (
      <Comp
        className={cn(buttonVariants({ variant, size, className }))}
        ref={ref}
        {...props}
      />
    );
  }
);
Button.displayName = 'Button';
```

---

## ❌ Common Anti-Patterns to Avoid

### State Management

```tsx
// ❌ BAD: Syncing derived state
const [fullName, setFullName] = useState(`${first} ${last}`);
useEffect(() => {
  setFullName(`${first} ${last}`);
}, [first, last]);

// ✅ GOOD: Derive directly
const fullName = `${first} ${last}`;

// ❌ BAD: Copying props to state
function Profile({ user }: Props) {
  const [name, setName] = useState(user.name); // Gets stale!
}

// ✅ GOOD: Use props directly
function Profile({ user }: Props) {
  return <div>{user.name}</div>;
}
```

### Effects

```tsx
// ❌ BAD: Missing cleanup
useEffect(() => {
  const id = setInterval(tick, 1000);
}, []);

// ✅ GOOD: Always cleanup
useEffect(() => {
  const id = setInterval(tick, 1000);
  return () => clearInterval(id);
}, []);

// ❌ BAD: Stale closure
useEffect(() => {
  socket.on('message', () => {
    console.log(count); // Always stale!
  });
}, []);

// ✅ GOOD: Use useEffectEvent
const onMessage = useEffectEvent(() => {
  console.log(count); // Always fresh
});

useEffect(() => {
  socket.on('message', onMessage);
  return () => socket.off('message', onMessage);
}, []);
```

### Performance

```tsx
// ❌ BAD: Premature optimization
const sum = useMemo(() => a + b, [a, b]);

// ✅ GOOD: Profile first, then optimize
const sorted = useMemo(
  () => hugeArray.toSorted(...),
  [hugeArray]
); // Actually expensive

// ❌ BAD: Inline objects to memo children
<MemoChild style={{ color: 'red' }} onClick={() => {}} />

// ✅ GOOD: Stable references
const style = useMemo(() => ({ color: 'red' }), []);
const handleClick = useCallback(() => {}, []);
<MemoChild style={style} onClick={handleClick} />
```

---

## 🌳 Decision Trees

### Component Type

```
Need interactivity (state, effects, events)?
├─ Yes → 'use client'
└─ No  → Server Component (default)

Client component too large?
├─ Yes → Extract only interactive part to 'use client'
└─ No  → Entire component as 'use client'
```

### State Management

```
What type of data?
├─ Server data        → React Query
├─ URL state          → nuqs / useSearchParams
├─ Form data          → react-hook-form + useActionState
├─ Local UI           → useState / useReducer
└─ Shared UI
    ├─ Rarely changes → Context
    └─ Frequent       → Zustand
```

### Testing Strategy

```
What to test?
├─ Pure function       → Unit test (Jest)
├─ Custom hook         → Unit test (renderHook)
├─ Component behavior  → Integration test (RTL)
├─ API integration     → Integration test (MSW)
├─ User journey        → E2E test (Playwright)
└─ Visual UI           → Visual regression (Playwright)
```

### Rendering Mode

```
Static content?           → SSG (default)
Static + periodic update? → ISR (revalidate = N)
Per-request data?         → SSR (dynamic = 'force-dynamic')
Static + dynamic parts?   → PPR ('use cache' + Suspense)
Heavy interactivity?      → CSR ('use client')
```

---

## ✅ Quality Checklist

Use this checklist before merging code:

### Architecture
- [ ] Server Components by default
- [ ] 'use client' only when necessary
- [ ] loading.tsx + error.tsx for routes
- [ ] Features properly encapsulated

### Type Safety
- [ ] No `any` types
- [ ] Zod validates external data
- [ ] DTO → ViewModel mapping
- [ ] Strict TypeScript enabled

### Performance
- [ ] Bundle size < 200KB per route
- [ ] Heavy components code-split
- [ ] Images optimized (next/image)
- [ ] React Query staleTime set
- [ ] Core Web Vitals passing

### Accessibility
- [ ] Semantic HTML
- [ ] Keyboard navigation works
- [ ] ARIA when necessary
- [ ] Color contrast 4.5:1+
- [ ] Screen reader tested

### Testing
- [ ] Unit tests for logic
- [ ] Integration tests for flows
- [ ] E2E for critical paths
- [ ] >80% coverage
- [ ] Tests use proper queries

### Code Quality
- [ ] Single Responsibility
- [ ] Effects have cleanup
- [ ] Stable keys for lists
- [ ] No premature optimization
- [ ] ESLint passing

---

## 📖 How to Use This Skill

### For New Features

1. Read relevant reference documents FIRST
2. Plan architecture (Server vs Client Components)
3. Define types with Zod schemas
4. Implement API layer (Server Actions + React Query)
5. Build UI components
6. Write tests (unit → integration → E2E)
7. Optimize performance
8. Ensure accessibility
9. Review with checklist

### For Code Review

1. Check against quality checklist
2. Verify reference docs followed
3. Test locally (keyboard nav, screen reader)
4. Run Lighthouse audit
5. Check bundle size impact

### For Debugging

1. Use React DevTools Profiler
2. Check Network tab for waterfalls
3. Verify proper caching (React Query DevTools)
4. Test error boundaries
5. Check accessibility tree

---

## 🚀 Getting Started

When you ask for help with frontend development, I will:

1. **Analyze your requirements** - Understand the feature/problem
2. **Read relevant reference docs** - Load applicable guides
3. **Design the solution** - Plan architecture and patterns
4. **Provide production code** - Following all best practices
5. **Include tests** - Unit, integration, or E2E as needed
6. **Ensure quality** - Performance, a11y, type safety

**Example Request:**
"Build a post creation form with validation, optimistic updates, and accessibility"

**I will:**
- Read 01-react-hooks.md (for useActionState, useOptimistic)
- Read 09-accessibility.md (for form a11y)
- Design with Server Actions + React Query
- Implement with Zod validation
- Add proper ARIA labels and error handling
- Include tests with MSW
- Ensure keyboard navigation works

This skill represents **senior-level, production-ready frontend engineering**. Every pattern is battle-tested in real applications serving millions of users.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/yoriichi-dang) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
