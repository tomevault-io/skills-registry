---
name: trpc
description: Implement tRPC APIs in a T3 stack (Next.js 15 + tRPC + TanStack React Query + Zod + Prisma + BetterAuth) using a domain router + controller architecture with strict typing and middleware-based authorization. Use when this capability is needed.
metadata:
  author: madsnyl
---

# tRPC API Architecture Skill (T3 stack)

You implement and refactor tRPC APIs in a **T3-style** project (Next.js 15, tRPC, TanStack React Query, Zod, Prisma, BetterAuth).
You follow the user's **Router + Controller** architecture and their procedure naming (`authorizedProcedure`).

## When to use this skill
- Creating new tRPC routers/procedures/endpoints
- Refactoring endpoints into controller files
- Adding Zod schemas + typing patterns
- Designing middleware-based authorization (workspace access, role gates)
- Client usage guidance (server components and client components)

## Canonical project patterns (must follow)

### Procedure types
Defined in `src/server/api/trpc.ts`:

- `publicProcedure`: no auth required; `ctx.session` / `ctx.user` may be null.
- `authorizedProcedure`: requires valid session; guarantees `ctx.user` and `ctx.session` are non-null; throws `UNAUTHORIZED` if not logged in.
- `authorizedAdminProcedure`: requires admin user; guarantees `ctx.user.isAdmin === true`; throws `UNAUTHORIZED` if not admin.

### Context shape
Every handler receives:
```ts
{
  session: Session | null,
  user: User | null,
  db: PrismaClient,
  headers: Headers,
}
```

### Router + Controller structure (domain-first)
Routers aggregate procedures; controllers implement business logic.

```
src/server/api/<domain>/
├── router.ts
├── controller/
│   ├── create.ts
│   ├── update.ts
│   ├── delete.ts
│   └── list.ts
└── middleware/
    └── access.ts
```

**Router example**
```ts
import { createTRPCRouter } from "~/server/api/trpc";
import create from "./controller/create";
import update from "./controller/update";
import _delete from "./controller/delete";

export const eventRouter = createTRPCRouter({
  create,
  update,
  delete: _delete, // reserved keyword workaround
});
```

**Controller example**
```ts
import { type z } from "zod";
import { TRPCError } from "@trpc/server";
import { authorizedProcedure, type Controller } from "~/server/api/trpc";
import { CreateEventInputSchema } from "~/schemas/event";
import { isWorkspaceAdminMiddleware } from "../middleware/access";

const handler: Controller<
  z.infer<typeof CreateEventInputSchema>,
  { id: string }
> = async ({ ctx, input }) => {
  await isAdminMiddleware(input.workspaceId, ctx);

  const created = await ctx.db.event.create({
    data: {
      workspaceId: input.workspaceId,
      title: input.title,
      startDate: input.startDate,
      endDate: input.endDate,
    },
    select: { id: true },
  });

  if (!created) {
    throw new TRPCError({ code: "INTERNAL_SERVER_ERROR" });
  }

  return created;
};

export default authorizedProcedure
  .input(CreateEventInputSchema)
  .mutation(handler);
```

## Best practices to enforce (tRPC + T3)

### 1) Always validate input with Zod
- Every procedure should have `.input(SomeSchema)` unless truly input-less.
- Keep schemas in `src/schemas/` and domain-group them (e.g. `src/schemas/event.ts`).

### 2) Keep handlers testable and small
- No inline 100-line `.query(async () => ...)`.
- Controllers should be pure business logic + calls to middleware + Prisma.

### 3) Authorization via middleware (separation of concerns)
- Do not mix permission checks inline.
- Middleware should be reusable and focused:
  - `hasAccessMiddleware` (any role)
- Middlewares should throw `TRPCError({ code: "FORBIDDEN" | "UNAUTHORIZED" })`.

tRPC supports middleware chaining and context extension; use it to keep procedures clean. (See references.)

### 4) Use transactions for multi-step mutations
- Use `ctx.db.$transaction(async (db) => { ... })` for atomic multi-write flows.
- Keep transactions short; avoid long reads inside write transactions.

### 5) Output typing rules
- Prefer types from the Prisma client
- If returning a subset, use `select` and export a derived type (from your Prisma type skill).

### 6) Error handling & formatting
- Throw `TRPCError` with consistent codes/messages.
- For shared, typed error metadata, implement an error formatter at the root router (tRPC supports typed error formatting). (See references.)

### 7) Next.js 15 + RSC guidance
- tRPC can work with React Server Components, but RSCs often remove the need for an API layer for reads.
- Use tRPC primarily for:
  - authenticated mutations
  - client-driven reads requiring caching/pagination
  - reuse across environments
Follow tRPC’s RSC integration guide when needed. (See references.)

## Client usage patterns (TanStack React Query)

Use `~/trpc/react` hooks (TanStack React Query) for interactive UIs:

```tsx
"use client";
import { api } from "~/trpc/react";

export function EventsManager({ workspaceId }: { workspaceId: string }) {
  const ruter = useRouter();

  const create = api.workspace.event.create.useMutation({
    onSuccess: async () => {
      await router.refresh();
    },
  });

  return (
    <button
      onClick={() => create.mutate({ workspaceId, title: "Demo", startDate: new Date(), endDate: new Date() })}
    >
      Create
    </button>
  );
}
```

## BetterAuth integration expectations
This skill assumes BetterAuth session/user are available in `ctx` (via `createContext`).
When the client must forward auth cookies/headers to the tRPC link, ensure headers include the session cookie (batch link headers). (See references — community notes.)

## Deliverables format (how you should respond)
When adding an endpoint, output:

1) **New/updated Zod schema** in `src/schemas/<domain>.ts`  
2) **Controller file**: `src/server/api/<domain>/controller/<name>.ts`  
3) **Router update**: `src/server/api/<domain>/router.ts`  
4) **Client usage snippet** (server component + client component hook example)  
5) Any required **middleware additions** and why

## Anti-patterns to avoid (hard rules)
- ❌ No inline large handlers inside `router.ts`
- ❌ No missing `.input()` validation for endpoints with input
- ❌ No auth checks scattered inside business logic; use middleware
- ❌ No multi-step writes without a transaction

## Additional resources

- For complete TRPC details, see [reference.md](@.claude/skills/trpc/reference.md)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/madsnyl) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
