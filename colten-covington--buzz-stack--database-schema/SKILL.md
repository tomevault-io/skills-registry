---
name: database-schema
description: Database schema design skill for Buzz Stack projects, with Prisma-focused patterns for relations, indexes, constraints, migrations, consistency, and schema evolution. Use when this capability is needed.
metadata:
  author: colten-covington
---

# Database Schema

## Overview

Buzz Stack itself is framework-first and database-agnostic, but production apps built on it commonly use **Postgres** (often via **Prisma**) plus a managed platform (e.g., Vercel Postgres). This skill provides a **schema-first** approach with clear patterns for:

- Designing tables/models, relations, and constraints
- Planning safe migrations (including zero-downtime “expand/contract”)
- Keeping data consistent (transactions, optimistic locking, idempotency)
- Preventing performance cliffs (N+1, missing indexes, poor cardinality)

This is written for teams that want **predictable evolvability** over “ship now, regret later”.

## Core Concepts

### 1) Prisma Schema Fundamentals (Models, Fields, Attributes)

Prisma schemas describe models (tables), fields (columns), and constraints via attributes.

```prisma
// prisma/schema.prisma

datasource db {
  provider  = "postgresql"
  url       = env("DATABASE_URL")
  directUrl = env("DATABASE_URL_DIRECT")
}

generator client {
  provider = "prisma-client-js"
}

model User {
  id        String   @id @default(cuid())
  email     String   @unique
  name      String
  createdAt DateTime @default(now())
  updatedAt DateTime @updatedAt
}
```

Key ideas:

- Prefer stable primary keys (`cuid()`/`uuid()`) over natural keys
- `@unique` is both a correctness constraint and an index
- Always include `createdAt` and `updatedAt` for auditability

---

### 2) Relations: 1:1, 1:N, N:M (and Choosing Ownership)

A 1:N relation (User → Post) uses a foreign key.

```prisma
model User {
  id    String @id @default(cuid())
  email String @unique

  posts Post[]
}

model Post {
  id        String   @id @default(cuid())
  title     String
  body      String
  createdAt DateTime @default(now())

  authorId String
  author   User   @relation(fields: [authorId], references: [id], onDelete: Cascade)

  @@index([authorId])
}
```

Guidelines:

- Put the foreign key on the “many” side
- Add an index on FK columns used in queries
- Be deliberate with `onDelete` (see Anti-patterns)

Many-to-many: explicit join tables are often better than implicit for extensibility.

```prisma
model User {
  id    String @id @default(cuid())
  email String @unique

  memberships ProjectMember[]
}

model Project {
  id          String          @id @default(cuid())
  name        String
  memberships ProjectMember[]
}

model ProjectMember {
  userId    String
  projectId String
  role      String
  joinedAt  DateTime @default(now())

  user    User    @relation(fields: [userId], references: [id], onDelete: Cascade)
  project Project @relation(fields: [projectId], references: [id], onDelete: Cascade)

  @@id([userId, projectId])
  @@index([projectId])
}
```

---

### 3) Indexes, Constraints, and Cardinality

Indexes are performance tools; constraints are correctness tools. Both matter.

Common primitives:

- `@unique` / `@@unique`: enforce uniqueness and create an index
- `@@index`: speed up lookups and joins
- Compound indexes for multi-column filters and sorting

Example: compound uniqueness for multi-tenant models.

```prisma
model Tenant {
  id   String @id @default(cuid())
  slug String @unique
}

model TenantUser {
  id       String @id @default(cuid())
  tenantId String
  email    String

  tenant Tenant @relation(fields: [tenantId], references: [id], onDelete: Cascade)

  @@unique([tenantId, email])
  @@index([tenantId])
}
```

Rule of thumb:

- Index every FK you join on
- For hot endpoints: index `(tenantId, createdAt)` when you frequently paginate by time
- Avoid adding indexes blindly; each index slows writes and increases storage

---

### 4) Normalization Strategies (1NF → BCNF) + When to Denormalize

Normalization reduces duplication and update anomalies.

- **1NF:** atomic fields, no repeating groups
- **2NF:** non-key fields depend on whole key (important for composite keys)
- **3NF:** non-key fields depend only on key (no transitive deps)
- **BCNF:** every determinant is a candidate key (stricter than 3NF)

Use denormalization when:

- Read performance needs outweigh write complexity
- You can tolerate eventual consistency
- You have clear backfill/rebuild paths

Example: denormalized counter.

```prisma
model Post {
  id            String @id @default(cuid())
  title         String
  commentCount  Int    @default(0) // denormalized

  comments Comment[]
}

model Comment {
  id     String @id @default(cuid())
  postId String
  post   Post   @relation(fields: [postId], references: [id], onDelete: Cascade)

  @@index([postId])
}
```

---

### 5) Migration Planning (Zero-Downtime + Rollback Safety)

In production, assume:

- migrations can be interrupted
- old and new app versions may run concurrently
- data backfills may take time

The safest general approach is **expand/contract**:

1. Expand schema (add nullable columns / new tables)
2. Deploy app that writes to both old + new (or writes new while reading old)
3. Backfill existing rows
4. Switch reads to new schema
5. Contract (drop old columns)

Example: add a non-null column with default without downtime.

```sql
-- 1) Expand: add nullable column
ALTER TABLE "User" ADD COLUMN "displayName" TEXT;

-- 2) Backfill in chunks (app job or SQL)
UPDATE "User" SET "displayName" = "name" WHERE "displayName" IS NULL;

-- 3) Contract: enforce NOT NULL after backfill
ALTER TABLE "User" ALTER COLUMN "displayName" SET NOT NULL;
```

Rollback principles:

- Keep migrations reversible when feasible
- Prefer additive changes first
- Avoid dropping columns in the same deploy you stop reading them

---

### 6) Data Consistency Patterns

You’ll encounter these choices:

- **Transactions:** strongest consistency; use for multi-row invariants
- **Optimistic locking:** avoid lost updates
- **Idempotency keys:** safe retries
- **Eventual consistency:** for async denormalized views

#### Transaction example (Prisma)

```typescript
import { PrismaClient } from "@prisma/client";

const prisma = new PrismaClient();

export async function transferCredits(input: {
  fromUserId: string;
  toUserId: string;
  amount: number;
}): Promise<void> {
  await prisma.$transaction(async (tx) => {
    const from = await tx.user.findUnique({ where: { id: input.fromUserId } });
    const to = await tx.user.findUnique({ where: { id: input.toUserId } });

    if (!from || !to) throw new Error("User not found");
    if (from.credits < input.amount) throw new Error("Insufficient credits");

    await tx.user.update({
      where: { id: from.id },
      data: { credits: { decrement: input.amount } },
    });

    await tx.user.update({
      where: { id: to.id },
      data: { credits: { increment: input.amount } },
    });
  });
}
```

---

### 7) Query Optimization (N+1 Prevention + Index Strategy)

Common causes of slow endpoints:

- N+1 fetching patterns
- Missing indexes on FK or sort keys
- Querying by non-selective columns

Example: avoid N+1 by eager loading.

```typescript
// ❌ N+1: one query per post
const posts = await prisma.post.findMany({ where: { published: true } });
for (const post of posts) {
  await prisma.comment.findMany({ where: { postId: post.id } });
}

// ✅ eager load
const postsWithComments = await prisma.post.findMany({
  where: { published: true },
  include: { comments: true },
});
```

---

## Patterns (10+)

### Pattern 1: Standard “Audit Columns” Everywhere

```prisma
model BaseAuditExample {
  id        String   @id @default(cuid())
  createdAt DateTime @default(now())
  updatedAt DateTime @updatedAt
}
```

If you can’t share model fragments, replicate the idea consistently.

---

### Pattern 2: Soft Deletes (When Hard Deletes Are Risky)

```prisma
model User {
  id        String    @id @default(cuid())
  email     String    @unique
  deletedAt DateTime?

  @@index([deletedAt])
}
```

Query pattern:

```typescript
const activeUsers = await prisma.user.findMany({ where: { deletedAt: null } });
```

---

### Pattern 3: Unique Constraints for Business Keys

```prisma
model Invite {
  id        String   @id @default(cuid())
  email     String
  token     String   @unique
  createdAt DateTime @default(now())
}
```

---

### Pattern 4: Multi-Tenant Scoping With Compound Indexes

```prisma
model Project {
  id       String @id @default(cuid())
  tenantId String
  name     String

  @@index([tenantId, name])
}
```

---

### Pattern 5: Optimistic Locking via Version Columns

```prisma
model Document {
  id      String @id @default(cuid())
  title   String
  content String
  version Int    @default(1)
}
```

Update pattern:

```typescript
async function updateDocument(input: {
  id: string;
  version: number;
  content: string;
}) {
  const updated = await prisma.document.updateMany({
    where: { id: input.id, version: input.version },
    data: { content: input.content, version: { increment: 1 } },
  });

  if (updated.count !== 1) {
    throw new Error("Conflict: document was updated by someone else");
  }
}
```

---

### Pattern 6: Outbox Table for Reliable Event Publishing

```prisma
model OutboxEvent {
  id        String   @id @default(cuid())
  topic     String
  payload   Json
  createdAt DateTime @default(now())
  sentAt    DateTime?

  @@index([sentAt, createdAt])
}
```

Flow:

- Write business change + outbox event in the same transaction
- Background worker publishes events and sets `sentAt`

---

### Pattern 7: “Expand/Contract” for Renames (Safe Schema Evolution)

Rename is really: add new, dual-write, backfill, swap reads, drop old.

```sql
-- Expand
ALTER TABLE "User" ADD COLUMN "full_name" TEXT;

-- Backfill
UPDATE "User" SET "full_name" = "name" WHERE "full_name" IS NULL;

-- Contract later (after app reads full_name)
-- ALTER TABLE "User" DROP COLUMN "name";
```

---

### Pattern 8: Backfill Jobs That Are Chunked + Idempotent

```typescript
async function backfillDisplayName(batchSize: number): Promise<number> {
  const users = await prisma.user.findMany({
    where: { displayName: null },
    take: batchSize,
    orderBy: { createdAt: "asc" },
    select: { id: true, name: true },
  });

  for (const user of users) {
    await prisma.user.update({
      where: { id: user.id },
      data: { displayName: user.name },
    });
  }

  return users.length;
}
```

---

### Pattern 9: Avoiding N+1 With `select` + `include`

```typescript
const projects = await prisma.project.findMany({
  where: { tenantId },
  select: {
    id: true,
    name: true,
    memberships: {
      select: { role: true, user: { select: { id: true, email: true } } },
    },
  },
});
```

---

### Pattern 10: Connection Pooling Awareness (Platform Reality)

Buzz Stack docs reference a managed Postgres pool endpoint and Prisma’s `directUrl`.

```prisma
datasource db {
  provider  = "postgresql"
  url       = env("DATABASE_URL")
  directUrl = env("DATABASE_URL_DIRECT")
}
```

Guideline:

- Use pooled `DATABASE_URL` for normal app queries
- Use `DIRECT` for migrations / long-running jobs

---

### Pattern 11: “Schema Freeze” During Critical Releases

Create a release policy:

- No breaking migrations after a “freeze date”
- Only additive changes allowed
- Any backfill must be reversible and monitored

---

### Pattern 12: Feature-Flagged Schema Usage

Schema changes often require coordinated rollout.

```typescript
function shouldUseNewColumn(flags: { useNewDisplayName: boolean }): boolean {
  return flags.useNewDisplayName;
}
```

Pair with expand/contract for safety.

---

## Anti-Patterns (5+)

### Anti-pattern 1: Cascading Deletes Everywhere

`onDelete: Cascade` is powerful and dangerous.

- It can delete huge subtrees unexpectedly
- It can amplify bugs into data loss

Prefer:

- soft deletes
- restricted deletes + explicit cleanup jobs

---

### Anti-pattern 2: Big-Bang Migrations

Deploying a migration that:

- rewrites a huge table
- adds NOT NULL without backfill
- drops columns immediately

…creates long locks and downtime.

Use expand/contract instead.

---

### Anti-pattern 3: Missing FK Indexes

Foreign keys used in joins must be indexed.

```prisma
model Comment {
  id     String @id @default(cuid())
  postId String
  post   Post   @relation(fields: [postId], references: [id])

  // ✅ should have this
  @@index([postId])
}
```

---

### Anti-pattern 4: Denormalize Without a Rebuild Plan

If you denormalize counters, cached strings, or derived columns, document:

- how to backfill
- how to re-compute
- what “source of truth” is

---

### Anti-pattern 5: Letting “JSON Everywhere” Replace Schema

A `Json` column is useful for:

- event payloads
- flexible metadata

It is not a replacement for:

- constraints
- queryable columns
- indexes

---

## Real-World Buzz Stack References

Buzz Stack’s docs already touch deployment + migration reality:

- connection pooling via Prisma schema `directUrl`
- zero-downtime rollback guidance

Use these as the baseline expectations in your app:

- See: ../../../docs/DEPLOYMENT-DEVOPS.md
- See: ../../../docs/SECURITY-VULNERABILITY.md (parameterized queries via Prisma)

## Cross-References

- Skills:
  - `devops-deployment`: ../devops-deployment/SKILL.md
  - `security-vulnerability`: ../security-vulnerability/SKILL.md
  - `api-design-contracts`: ../api-design-contracts/SKILL.md
  - `architecture-design`: ../architecture-design/SKILL.md

- Docs:
  - Deployment & rollback: ../../../docs/DEPLOYMENT-DEVOPS.md
  - Security & injection mitigation: ../../../docs/SECURITY-VULNERABILITY.md
  - Architecture overview: ../../../docs/ARCHITECTURE.md

## Quick Checklist

- Are all relations explicit and indexed?
- Do uniqueness constraints reflect business invariants?
- Can every migration be rolled out with expand/contract?
- Is there a backfill plan and a rollback plan?
- Are hot queries explainable and index-backed?

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/colten-covington) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
