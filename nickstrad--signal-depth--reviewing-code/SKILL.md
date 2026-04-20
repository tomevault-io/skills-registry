---
name: reviewing-code
description: Reviews code changes in the Nick Stack codebase, checking for tech stack patterns, security, type safety, and best practices. Use when reviewing PRs, commits, or code changes. Use when this capability is needed.
metadata:
  author: nickstrad
---

You are reviewing code in a Next.js full-stack application with the following tech stack:

## Tech Stack
- **Framework**: Next.js 16.1 (App Router)
- **Language**: TypeScript
- **Database**: PostgreSQL with Prisma ORM
- **API**: tRPC for type-safe API routes
- **Auth**: Better Auth (email + Google OAuth)
- **UI**: shadcn/ui + Radix UI + Tailwind CSS 4
- **Forms**: React Hook Form + Zod validation
- **State**: TanStack React Query

## Project Structure Conventions
```
src/
├── app/                    # Next.js App Router pages
│   ├── (auth)/            # Auth route group
│   ├── api/               # API routes (tRPC, auth)
│   └── [feature]/         # Feature pages
├── components/
│   ├── app/               # App-specific components
│   └── ui/                # shadcn/ui components
├── features/              # Feature modules
│   └── [feature]/
│       ├── components/    # Feature components
│       ├── hooks/         # Feature hooks
│       └── server/        # Server-side logic
│           ├── router.ts  # tRPC router
│           ├── params.ts  # Search params loader
│           └── prefetch.ts # Data prefetching
├── lib/                   # Shared utilities
│   ├── auth.ts           # Better Auth config
│   ├── db.ts             # Prisma client
│   └── utils/            # Helper functions
└── trpc/                  # tRPC setup
    ├── routers/          # API routers
    ├── client.tsx        # Client-side setup
    └── server.tsx        # Server-side setup
```

## Review Checklist

### 1. Next.js App Router Patterns
- [ ] Server components by default, client components marked with "use client"
- [ ] Async server components for data fetching
- [ ] Proper use of Suspense boundaries with loading states
- [ ] Error boundaries for error handling
- [ ] Server actions in server components
- [ ] Client-side interactivity in client components only
- [ ] Search params using `nuqs` library with type-safe loaders

**Example Pattern:**
```tsx
// Server Component (default)
export default async function Page({ searchParams }: Props) {
  const params = await paramsLoader(searchParams);
  prefetchData(params); // Prefetch on server

  return (
    <HydrateClient>
      <ErrorBoundary fallback={<ErrorComponent />}>
        <Suspense fallback={<LoadingComponent />}>
          <ClientComponent />
        </Suspense>
      </ErrorBoundary>
    </HydrateClient>
  );
}
```

### 2. tRPC Patterns
- [ ] Routers organized in `src/trpc/routers/`
- [ ] Feature routers in `src/features/[feature]/server/router.ts`
- [ ] Input validation with Zod schemas
- [ ] Proper use of `.query()` for reads, `.mutation()` for writes
- [ ] Type-safe API calls using `trpc.feature.method.useQuery()`
- [ ] Error handling in procedures
- [ ] Pagination using standard pattern from constants

**Example Router:**
```typescript
export const featureRouter = createTRPCRouter({
  getMany: baseProcedure
    .input(z.object({
      page: z.number().default(PAGINATION.DEFAULT_PAGE),
      pageSize: z.number().default(PAGINATION.DEFAULT_PAGE_SIZE),
      search: z.string().default(""),
    }))
    .query(async ({ input }) => {
      // Implementation
    }),

  create: baseProcedure
    .input(entitySchema)
    .mutation(async ({ input }) => {
      // Implementation
    }),
});
```

### 3. Prisma Patterns
- [ ] Schema follows Better Auth conventions (User, Session, Account, Verification)
- [ ] Proper relations and cascades (onDelete: Cascade)
- [ ] Indexes on foreign keys and frequently queried fields
- [ ] Generated types imported from `@/generated/prisma/client`
- [ ] Prisma client imported from `@/lib/db`
- [ ] Efficient queries (avoid N+1, use Promise.all for parallel queries)
- [ ] Case-insensitive search with `{ contains: search, mode: "insensitive" }`

**Common Pattern:**
```typescript
const [items, total] = await Promise.all([
  prisma.model.findMany({ where, skip, take, orderBy }),
  prisma.model.count({ where }),
]);
```

### 4. Authentication & Security
- [ ] Protected routes check authentication status
- [ ] API procedures use auth context when needed
- [ ] No sensitive data in client components
- [ ] Environment variables properly typed and validated
- [ ] No hardcoded secrets or credentials
- [ ] Proper CORS configuration
- [ ] SQL injection prevented (Prisma handles this)
- [ ] XSS prevention (React escapes by default, but check dangerouslySetInnerHTML)
- [ ] Input validation on all user inputs (Zod schemas)

**Auth Check Pattern:**
```typescript
// Better Auth config in lib/auth.ts
export const auth = betterAuth({
  baseURL: process.env.BETTER_AUTH_URL,
  database: prismaAdapter(prisma, { provider: "postgresql" }),
  socialProviders: { google: { /* ... */ } },
  emailAndPassword: { enabled: true },
});
```

### 5. Form Handling
- [ ] React Hook Form with zodResolver
- [ ] Zod schema validation matching backend schema
- [ ] Form components from shadcn/ui
- [ ] Loading states during submission (isPending)
- [ ] Error message display with FormMessage
- [ ] Form reset after successful submission
- [ ] Disabled inputs during submission
- [ ] Proper TypeScript types for form values

**Form Pattern:**
```tsx
const formSchema = z.object({
  field: z.string().min(1, "Required"),
});

type FormValues = z.infer<typeof formSchema>;

const form = useForm<FormValues>({
  resolver: zodResolver(formSchema),
  defaultValues: { /* ... */ },
});

const mutation = useCreateMutation();

const onSubmit = (data: FormValues) => {
  mutation.mutate(data, {
    onSuccess: () => form.reset(),
  });
};
```

### 6. Component Patterns
- [ ] Feature components in `src/features/[feature]/components/`
- [ ] Separate Loading, Error, and Empty states
- [ ] UI components from `@/components/ui/` (shadcn/ui)
- [ ] Proper TypeScript prop types
- [ ] Client components marked with "use client"
- [ ] Hooks organized in feature hooks/ folders
- [ ] No business logic in UI components
- [ ] Accessible components (Radix handles most of this)

**Component Organization:**
```
features/entity/
├── components/
│   ├── entity-form.tsx
│   ├── entity-list.tsx
│   ├── entity-list-loading.tsx
│   ├── entity-list-error.tsx
│   └── entity-editor.tsx
├── hooks/
│   └── use-entities.ts
└── server/
    ├── router.ts
    ├── params.ts
    └── prefetch.ts
```

### 7. Type Safety
- [ ] No `any` types (use `unknown` if necessary)
- [ ] Proper TypeScript generics usage
- [ ] Import types from Prisma generated client
- [ ] Zod schemas for runtime validation
- [ ] tRPC provides end-to-end type safety
- [ ] Proper null/undefined handling
- [ ] Type imports vs value imports

### 8. Performance & Optimization
- [ ] Server-side data prefetching for instant page loads
- [ ] Pagination for large datasets
- [ ] Database query optimization (indexes, efficient queries)
- [ ] React Query caching configured properly
- [ ] Images optimized with Next.js Image component
- [ ] Lazy loading where appropriate
- [ ] Avoid unnecessary re-renders

**Prefetch Pattern:**
```typescript
// src/features/entity/server/prefetch.ts
export const prefetchEntities = (params: Params) => {
  void api.entities.getMany.prefetch(params);
};
```

### 9. Error Handling
- [ ] Error boundaries for component errors
- [ ] Try-catch in async operations
- [ ] Meaningful error messages
- [ ] Error states in UI
- [ ] Proper HTTP status codes in API
- [ ] Logging for debugging

### 10. Common Issues to Flag

**❌ Bad:**
```tsx
// Server component fetching in client component
"use client";
export default function Page() {
  const data = await fetch(...); // ❌ Can't use await in client component
}

// Missing input validation
export const router = createTRPCRouter({
  create: baseProcedure.mutation(async ({ input }) => {
    // ❌ No input schema validation
    return prisma.model.create({ data: input });
  }),
});

// Mixing server and client code
"use client";
import prisma from "@/lib/db"; // ❌ Can't use Prisma in client component
```

**✅ Good:**
```tsx
// Proper server component
export default async function Page() {
  const data = await fetchData(); // ✅ Async in server component
  return <ClientComponent data={data} />;
}

// Validated input
export const router = createTRPCRouter({
  create: baseProcedure
    .input(z.object({ name: z.string() })) // ✅ Validated
    .mutation(async ({ input }) => {
      return prisma.model.create({ data: input });
    }),
});

// Proper separation
// server.ts
export async function getData() {
  return prisma.model.findMany();
}

// component.tsx
"use client";
import { trpc } from "@/trpc/client";
export function Component() {
  const { data } = trpc.model.getMany.useQuery();
}
```

## Review Output Format

Structure your review as follows:

### Summary
[High-level assessment of the changes]

### Critical Issues 🚨
[Security vulnerabilities, breaking changes, major bugs]

### Improvements Needed ⚠️
[Non-critical issues, code smells, pattern violations]

### Suggestions 💡
[Optional improvements, refactoring opportunities]

### Positive Highlights ✅
[Good patterns, well-implemented features]

### Testing Recommendations
[What should be tested based on the changes]

---

Remember: This codebase values type safety, separation of concerns, and following established patterns. Flag deviations from these principles.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/nickstrad) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
