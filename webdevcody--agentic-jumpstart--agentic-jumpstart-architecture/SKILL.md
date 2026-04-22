---
name: agentic-jumpstart-architecture
description: Layered architecture patterns for TanStack Start applications with data access, use cases, and server functions. Use when designing features, organizing code, understanding project structure, or when the user mentions architecture, layers, structure, organization, or patterns. Use when this capability is needed.
metadata:
  author: webdevcody
---

# Layered Architecture

## Overview

The codebase follows a strict 3-layer architecture:

```
┌─────────────────────────────────────┐
│     Routes & Components             │  UI Layer
│     /src/routes/                    │
└─────────────────┬───────────────────┘
                  │
                  ▼
┌─────────────────────────────────────┐
│     Server Functions                │  API Layer
│     /src/fn/                        │
└─────────────────┬───────────────────┘
                  │
                  ▼
┌─────────────────────────────────────┐
│     Use Cases                       │  Business Logic
│     /src/use-cases/                 │
└─────────────────┬───────────────────┘
                  │
                  ▼
┌─────────────────────────────────────┐
│     Data Access                     │  Database
│     /src/data-access/               │
└─────────────────────────────────────┘
```

**Critical Rule**: Server functions should NEVER import Drizzle objects directly. Always call use cases.

## Layer Responsibilities

### Data Access Layer (`/src/data-access/`)

Pure database operations with no business logic.

**Naming**: `verbNoun` (e.g., `createUser`, `getSegmentById`)

```typescript
// src/data-access/users.ts
import { database } from "~/db";
import { users } from "~/db/schema";
import { eq } from "drizzle-orm";
import type { User, UserCreate } from "~/db/schema";

export async function getUserById(id: number) {
  const result = await database
    .select()
    .from(users)
    .where(eq(users.id, id))
    .limit(1);
  return result[0];
}

export async function createUser(user: UserCreate) {
  const result = await database.insert(users).values(user).returning();
  return result[0];
}

export async function updateUser(id: number, user: Partial<UserCreate>) {
  const result = await database
    .update(users)
    .set({ ...user, updatedAt: new Date() })
    .where(eq(users.id, id))
    .returning();
  return result[0];
}
```

### Use Cases Layer (`/src/use-cases/`)

Business logic and orchestration. Can call data access functions and other use cases.

**Naming**: `verbNounUseCase` (e.g., `createUserUseCase`, `enrollInCourseUseCase`)

```typescript
// src/use-cases/users.ts
import { getUserById, createUser, updateUser } from "~/data-access/users";
import { PublicError } from "./errors";
import type { UserCreate } from "~/db/schema";

export async function createUserUseCase(data: UserCreate) {
  // Business logic validation
  const existing = await getUserByEmail(data.email);
  if (existing) {
    throw new PublicError("Email already in use");
  }

  // Create user
  return createUser(data);
}

export async function updateUserProfileUseCase(
  userId: number,
  data: { name: string; bio?: string }
) {
  const user = await getUserById(userId);
  if (!user) {
    throw new PublicError("User not found");
  }

  return updateUser(userId, data);
}
```

### Server Functions Layer (`/src/fn/`)

HTTP endpoints that call use cases. Handle authentication, input validation, and response formatting.

**Naming**: `verbNounFn` (e.g., `createUserFn`, `updateProfileFn`)

```typescript
// src/fn/users.ts
import { createServerFn } from "@tanstack/react-start";
import { authenticatedMiddleware, adminMiddleware } from "~/lib/auth";
import { z } from "zod";
import { updateUserProfileUseCase, getUserProfileUseCase } from "~/use-cases/users";

export const updateProfileFn = createServerFn()
  .middleware([authenticatedMiddleware])
  .inputValidator(
    z.object({
      name: z.string().min(1).max(100),
      bio: z.string().max(500).optional(),
    })
  )
  .handler(async ({ data, context }) => {
    return updateUserProfileUseCase(context.userId, data);
  });

export const getUserProfileFn = createServerFn()
  .middleware([authenticatedMiddleware])
  .handler(async ({ context }) => {
    return getUserProfileUseCase(context.userId);
  });
```

## Project Structure

```
src/
├── routes/                 # File-based routing
│   ├── _layout.tsx        # Root layout
│   ├── index.tsx          # Home page
│   ├── courses/
│   │   ├── route.tsx      # /courses page
│   │   ├── $courseId/
│   │   │   └── route.tsx  # /courses/:courseId page
│   │   └── -components/   # Route-specific components
│   │       ├── CourseCard.tsx
│   │       └── CourseList.tsx
│   └── admin/
│       ├── route.tsx
│       └── -components/
├── components/            # Shared components
│   ├── ui/               # shadcn/ui components
│   │   ├── button.tsx
│   │   ├── card.tsx
│   │   └── dialog.tsx
│   ├── page.tsx          # Page layout component
│   └── DefaultCatchBoundary.tsx
├── fn/                    # Server functions
│   ├── users.ts
│   ├── auth.ts
│   └── segments.ts
├── use-cases/            # Business logic
│   ├── users.ts
│   ├── segments.ts
│   └── errors.ts
├── data-access/          # Database queries
│   ├── users.ts
│   └── segments.ts
├── db/                   # Database config
│   ├── index.ts         # Database connection
│   ├── schema.ts        # Drizzle schema
│   ├── migrate.ts       # Migration runner
│   └── seed.ts          # Seed data
├── hooks/               # React hooks
│   ├── useAuth.ts
│   └── mutations/       # Mutation hooks
│       └── useUpdateProfile.ts
├── lib/                 # Utilities
│   ├── auth.ts         # Auth middleware
│   └── utils.ts        # cn() utility
├── config/             # Configuration
│   └── index.ts        # Magic numbers, feature flags
└── utils/              # Helpers
    ├── env.ts          # Environment variables
    └── storage.ts      # S3/R2 utilities
```

## Route Protection

### Admin Routes

```typescript
import { createFileRoute } from "@tanstack/react-router";
import { assertIsAdminFn } from "~/fn/auth";

export const Route = createFileRoute("/admin/users")({
  beforeLoad: () => assertIsAdminFn(),
  component: AdminUsersPage,
});
```

### Authenticated Routes

```typescript
import { createFileRoute, redirect } from "@tanstack/react-router";

export const Route = createFileRoute("/dashboard")({
  beforeLoad: async ({ context }) => {
    if (!context.user) {
      throw redirect({ to: "/login" });
    }
  },
  component: DashboardPage,
});
```

## Error Handling

### PublicError Pattern

```typescript
// src/use-cases/errors.ts
export class PublicError extends Error {
  constructor(message: string) {
    super(message);
    this.name = "PublicError";
  }
}

export class NotFoundError extends PublicError {
  constructor(resource: string) {
    super(`${resource} not found`);
  }
}

export class UnauthorizedError extends PublicError {
  constructor(message = "Unauthorized") {
    super(message);
  }
}
```

### Using in Use Cases

```typescript
export async function getSegmentByIdUseCase(id: number) {
  const segment = await getSegmentById(id);
  if (!segment) {
    throw new NotFoundError("Segment");
  }
  return segment;
}
```

## Configuration Management

### Magic Numbers in Config

Never hard code magic numbers. Put them in `/src/config/`:

```typescript
// src/config/index.ts
export const MAX_FILE_SIZE = 500 * 1024 * 1024; // 500MB
export const MAX_TITLE_LENGTH = 100;
export const PAGINATION_DEFAULT_LIMIT = 20;
export const SESSION_EXPIRY_DAYS = 30;

// Feature flags
export const FEATURE_FLAGS = {
  enableNewCheckout: false,
  enableAIFeatures: true,
};
```

## Code Quality Rules

1. **File Size Limit**: Never let a file exceed 1,000 lines. Split into smaller modules.

2. **No Magic Numbers**: Consolidate at top of file or in `/src/config/`.

3. **Naming Conventions**:
   - Data access: `verbNoun`
   - Use cases: `verbNounUseCase`
   - Server functions: `verbNounFn`

4. **Import Order**:
   ```typescript
   // External packages
   import { createServerFn } from "@tanstack/react-start";
   import { z } from "zod";

   // Internal - path alias
   import { authenticatedMiddleware } from "~/lib/auth";
   import { updateUserUseCase } from "~/use-cases/users";
   import type { User } from "~/db/schema";
   ```

5. **Path Alias**: Use `~/` for imports from `/src/`:
   ```typescript
   import { Button } from "~/components/ui/button";
   ```

## Architecture Checklist

- [ ] Server functions call use cases, not data-access
- [ ] Use cases contain business logic
- [ ] Data access contains only database operations
- [ ] Route-specific components in `-components/` subdirectory
- [ ] Shared components in `/src/components/`
- [ ] Magic numbers in `/src/config/`
- [ ] Proper naming conventions used
- [ ] Files under 1,000 lines
- [ ] Path alias `~/` used for imports
- [ ] Protected routes use `beforeLoad`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/webdevcody) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
