---
name: prisma-database-querying
description: Query PostgreSQL with Prisma 7 efficiently, using Prisma Client for mutations and selectively using raw SQL for complex/performant reads (SELECT/COUNT) when appropriate. Use when this capability is needed.
metadata:
  author: madsnyl
---

# Prisma 7 Database Querying (PostgreSQL)

You are an expert in **efficient querying** with Prisma 7 for Postgres.

## Activation cues
Use this skill when the user asks about:
- Prisma `findMany/findFirst/findUnique`, `include/select`, filtering, ordering, pagination
- transactions, concurrency, batching
- performance optimization, N+1 issues, large reads
- when/how to use `$queryRaw` / `$executeRaw`
- counts/aggregations/grouping where ORM becomes awkward or slow

## Default policy (important)
- **Mutations (create/update/delete/upsert):** use **Prisma Client ORM** by default.
- **Reads (SELECT/COUNT/analytics):**
  - Use Prisma Client first.
  - Switch to **raw SQL** when Prisma cannot express the query cleanly, or when SQL can significantly improve performance (CTEs, window functions, custom joins, partial indexes usage, advanced grouping).

## Read patterns to prefer in Prisma Client
- Always scope fields using `select` (or narrowly scoped `include`) to avoid overfetching.
- Use cursor-based pagination for large tables:
  - `take`, `skip` only for small datasets; cursor for high-scale.
- Use `distinct`, `groupBy`, aggregates where they fit.
- Avoid N+1: query relations with `include` or two-step queries with `in` filters.

## Raw SQL rules
Use Prisma’s **parameterized** raw queries:
- `$queryRaw` for SELECT-like reads.
- `$executeRaw` for commands that return affected rows (never for SELECT).

Never build SQL strings from untrusted input. If you must do dynamic SQL, build the *structure* from safe enums/whitelists and pass user data as parameters.

(See Prisma raw SQL docs in `references/PRISMA7_CORE_REFERENCES.md`.)

## Transaction guidance
- Use `$transaction` for multi-step writes that must be atomic.
- Prefer short transactions; avoid long-running SELECTs inside write transactions unless required.

## Output format
When the user asks for a query, provide:
1. The recommended Prisma Client query (or raw SQL if justified)
2. Notes on indexes and expected query plan assumptions
3. Pagination strategy if results can be large

## Examples

### Example: efficient list endpoint with cursor pagination
```ts
// Input: { workspaceId, cursorId?: string, take?: number }
const take = Math.min(input.take ?? 50, 200);

const items = await prisma.project.findMany({
  where: { workspaceId: input.workspaceId },
  orderBy: { createdAt: "desc" },
  take: take + 1,
  ...(input.cursorId
    ? { cursor: { id: input.cursorId }, skip: 1 }
    : {}),
  select: {
    id: true,
    name: true,
    slug: true,
    createdAt: true,
  },
});

const hasNextPage = items.length > take;
const page = hasNextPage ? items.slice(0, take) : items;
const nextCursor = hasNextPage ? page[page.length - 1]!.id : null;
```

### Example: COUNT with complex join via raw SQL (read path)
```ts
import { Prisma } from "@prisma/client";

const rows = await prisma.$queryRaw<{ total: bigint }[]>`
  SELECT COUNT(*)::bigint AS total
  FROM "Project" p
  JOIN "Workspace" w ON w.id = p."workspaceId"
  WHERE w.id = ${input.workspaceId}
    AND p."createdAt" >= ${input.since}
`;

const total = Number(rows[0]?.total ?? 0n);
```

### Example: mutation stays in Prisma Client (write path)
```ts
await prisma.project.update({
  where: { id: input.projectId },
  data: { name: input.name, slug: input.slug },
});
```

## Common pitfalls to warn about
- Mixing `select` and `include` incorrectly: choose one strategy; if you need relations and partial scalars, structure the query accordingly.
- Using `$executeRaw` for SELECT: it returns affected rows, not data.
- Using `skip`/`take` offsets on large tables: can become slow; use cursor.

## Additional resources

- For complete Prisma docs details, see [reference.md](@.claude/skills/prisma/reference.md)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/madsnyl) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
