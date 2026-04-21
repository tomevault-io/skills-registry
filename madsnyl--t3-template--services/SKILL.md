---
name: query-service
description: Create and maintain server-side Prisma “service” functions for SELECT and COUNT used by Next.js Server Components, organized by domain under services/, exported via index.ts, and returning strongly typed Prisma payload types (referencing the Prisma type-settings skill). Use when this capability is needed.
metadata:
  author: madsnyl
---

# Services: Server-side Prisma Queries for Server Components

You implement **server-only service functions** that run Prisma **SELECT and COUNT** queries for Next.js Server Components (RSC).

These services live in `@src/services/`, are **domain-scoped**, have **easy-to-understand names**, and are re-exported via `@src/services/index.ts`. They use the **Prisma type patterns** defined in the [prisma types skill](@.claude/skills/types/SKILL.md)(Prisma.validator + GetPayload) for return types.

## When to use this skill
Use this skill when the user asks to:
- add/refactor server-side Prisma reads used in Server Components
- implement list pages with pagination, filtering, sorting, and total counts
- organize server data access in a `services/` folder
- ensure return types match the project’s `types/<domain>/...` patterns

## Folder & naming conventions
- Services live here: `@src/services/<domain>/...`
- File names are domain-specific and descriptive:
  - `@src/services/users/get-users.ts`
  - `@src/services/events/get-event-previews.ts`
  - `@src/services/workspaces/get-workspace-members.ts`
- Exported in: `@src/services/index.ts`
- Each service function name is a clear verb phrase:
  - `getUsers`, `getWorkspaceMembers`, `getEventPreviews`, `countUsers`

## Hard rules
1. **Server-only**: add `"use server"` at the top of each service file.
2. **Read-only responsibilities**: these services are for **SELECT/COUNT** for server rendering.
   - Mutations belong in tRPC controllers (unless the user explicitly wants a server action for a mutation).
3. **Type-safe outputs**: the returned model shapes must come from the **Prisma type-settings skill**:
   - Define reusable `select` / `include` objects in `types/<domain>/...`
   - Derive payload types via `Prisma.<Model>GetPayload<...>`
4. **Parallelize data + count**: for paginated lists, fetch `findMany` and `count` in `Promise.all`.
5. **Avoid overfetching**: always use `select` (preferred) or strict `include`.
6. **Deterministic ordering**: if sorting by a non-unique field (e.g. `createdAt`), add a tie-breaker order by `id` to keep pagination stable.
7. **Validate/whitelist sorting keys**: never pass arbitrary `sortBy` directly into `orderBy` without a whitelist.
8. **Performance-aware counts**: use `db.<model>.count({ where })` for simple counts; if count becomes complex and slow, consider raw SQL **read** optimization per Prisma querying skill (but keep this as an exception and keep it parameterized).

## Service function structure
Each service file should typically contain:
- `"use server"`
- `db` import from `~/server/db`
- An `Options` interface (only if needed)
- `where` builder (search/filter)
- `orderBy` builder with a whitelist
- `Promise.all([findMany, count])`
- Return a **typed** result object

### Recommended return shape for list endpoints
```ts
{
  items: T[];
  totalCount: number;
  page: number;
  pageSize: number;
  totalPages: number;
}
````

Use domain-appropriate names (`users`, `events`) if that improves clarity, but keep the structure consistent.

## Typing: reference the Prisma type-settings skill

Do **not** hand-write response shapes for Prisma models. Instead:

### 1) Define a reusable select in `@src/types/<domain>/...`

Example:

* `src/types/users/user.select.ts`
* `src/types/users/user.types.ts`

```ts
// src/types/users/user.select.ts
import { Prisma } from "@prisma/client";

export const userListSelect = Prisma.validator<Prisma.UserSelect>()({
  id: true,
  name: true,
  email: true,
  emailVerified: true,
  isAdmin: true,
  createdAt: true,
});
```

You can read more about this in the [types skill](@.claude/skills/types/SKILL.md)

```ts
// src/types/users/user.types.ts
import { Prisma } from "@prisma/client";
import { userListSelect } from "./user.select";

export type UserListItem = Prisma.UserGetPayload<{
  select: typeof userListSelect;
}>;
```

### 2) Use those exports in the service and type the return

```ts
// src/services/users/get-users.ts
"use server";

import { db } from "~/server/db";
import { userListSelect } from "~/types/users/user.select";
import type { UserListItem } from "~/types/users/user.types";

interface GetUsersOptions {
  page?: number;
  pageSize?: number;
  search?: string;
  sortBy?: "createdAt" | "name" | "email"; // whitelist
  sortOrder?: "asc" | "desc";
}

type GetUsersResult = {
  users: UserListItem[];
  totalCount: number;
  page: number;
  pageSize: number;
  totalPages: number;
};

export async function getUsers(options: GetUsersOptions = {}): Promise<GetUsersResult> {
  const {
    page = 1,
    pageSize = 10,
    search = "",
    sortBy = "createdAt",
    sortOrder = "desc",
  } = options;

  const safePage = Math.max(1, page);
  const safePageSize = Math.min(Math.max(1, pageSize), 200);
  const skip = (safePage - 1) * safePageSize;

  const where = search
    ? {
        OR: [
          { name: { contains: search, mode: "insensitive" as const } },
          { email: { contains: search, mode: "insensitive" as const } },
        ],
      }
    : {};

  // Stable ordering: requested field + id tie-breaker
  const orderBy = [{ [sortBy]: sortOrder } as const, { id: "asc" as const }];

  const [users, totalCount] = await Promise.all([
    db.user.findMany({
      where,
      select: userListSelect,
      orderBy,
      skip,
      take: safePageSize,
    }),
    db.user.count({ where }),
  ]);

  return {
    users,
    totalCount,
    page: safePage,
    pageSize: safePageSize,
    totalPages: Math.ceil(totalCount / safePageSize),
  };
}
```

## Index exports

Every domain service must be exported via `src/services/index.ts`:

```ts
// src/services/index.ts
export * from "./users/get-users";
export * from "./events/get-event-previews";
```

If you also keep per-domain `index.ts` files, export them from the root.

## Usage in Next.js Server Components

In a Server Component:

```tsx
import { getUsers } from "~/services";

export default async function UsersPage({ searchParams }: { searchParams: Record<string, string | string[]> }) {
  const page = Number(searchParams.page ?? 1);

  const data = await getUsers({
    page,
    pageSize: 20,
    search: typeof searchParams.q === "string" ? searchParams.q : "",
    sortBy: "createdAt",
    sortOrder: "desc",
  });

  return (
    <div>
      <div>Total: {data.totalCount}</div>
      {/* render data.users */}
    </div>
  );
}
```

## Advanced guidance

### Sorting whitelist (required)

Never do:

```ts
orderBy: { [sortBy]: sortOrder }
```

unless `sortBy` is a union of known keys (or validated via a whitelist map).
Preferred pattern:

```ts
const SORT_KEYS = {
  createdAt: "createdAt",
  name: "name",
  email: "email",
} as const;

type SortBy = keyof typeof SORT_KEYS;
```

### Search performance

For large tables:

* Ensure indexes exist for high-selectivity filters.
* Consider full-text search or trigram indexes in Postgres if `contains` search becomes slow (only if the user asks for scaling guidance).

### When count is too slow

If `count` becomes a bottleneck (complex filters/joins), you may:

* keep `findMany` in Prisma
* use **parameterized raw SQL** for the count query (read-only) per the Prisma querying skill
* document why this exception is used

## Output format when implementing a new service

When asked to create a service, output:

1. `types/<domain>/...` select + payload type (if missing)
2. `services/<domain>/<service>.ts` implementation
3. `services/index.ts` export
4. Example Server Component usage

## Cross-skill references

* [**Prisma type-settings skill**](@.claude/types/SKILL.md): all service return shapes that include Prisma model data must be typed via `Prisma.validator()` + `GetPayload` in `types/<domain>/...`.
* [**Prisma database-querying skill**](@.claude/queries/SKILL.md): raw SQL is acceptable for SELECT/COUNT only when Prisma cannot express the query efficiently; mutations stay in Prisma Client or tRPC controllers unless specified.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/madsnyl) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
