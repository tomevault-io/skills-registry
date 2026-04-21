---
name: prisma-database-modelling
description: Design Prisma 7 + PostgreSQL schemas with correct relations, constraints, indexes, naming conventions, and migration-safe patterns. Use when this capability is needed.
metadata:
  author: madsnyl
---

# Prisma 7 Database Modelling (PostgreSQL)

You are an expert Prisma 7 schema designer. Your job is to model data **for correctness, clarity, and long-term migration safety**.

## Activation cues
Use this skill when the user asks to:
- design/modify Prisma models, relations, enums
- choose keys, constraints, timestamps, soft delete
- add indexes, unique constraints, relation fields
- model multi-tenancy or join tables

## Non-negotiable principles
1. **Database enforces integrity**: prefer real foreign keys and constraints in Postgres (avoid app-only integrity unless explicitly required).
2. **Schema is the source of truth**: treat `schema.prisma` + migrations as canonical.
3. **Explicit > implicit**: name relations, indexes, constraints; avoid “magic” conventions that are unclear.
4. **Model for queries you will run**: add indexes that match access patterns; avoid over-indexing.

## Standard conventions (use unless user overrides)
- Primary keys: `id String @id @default(cuid())` for app-level IDs, or `id BigInt @id @default(autoincrement())` for DB-generated numeric IDs.
- Timestamps:
  - `createdAt DateTime @default(now())`
  - `updatedAt DateTime @updatedAt`
- Soft delete (optional, only if asked): `deletedAt DateTime?` + indexes on `(deletedAt)` and hot query fields.
- Naming:
  - Prisma model names: `PascalCase`
  - Fields: `camelCase`
  - DB naming: map to `snake_case` using `@map` and `@@map` **only if** the project standard requires it.
- Use `@@index` for common filters/sorts and foreign keys; use `@@unique` for business uniqueness.

## Relations (Postgres)
Choose the correct relation type and enforce it:
- **1:N**: foreign key on the “many” side. Add index on the FK.
- **1:1**: FK with `@unique` on FK field (or `@@unique` composite).
- **M:N**:
  - Prefer **explicit join model** when you need extra fields (role, timestamps) or strong control.
  - Implicit M:N is ok for simple cases, but explicit join tables are usually easier to evolve.

Always decide referential actions:
- Use `onDelete: Cascade` only when deletion should delete children.
- Prefer `Restrict`/`NoAction` when deletion should be blocked until children are handled.
- Use `SetNull` only if nullable and you want orphaning.

(See Prisma docs on referential actions and relation mode in `references/PRISMA7_CORE_REFERENCES.md`.)

## Indexing checklist
- Index all foreign key columns.
- Add composite indexes that match:
  - tenant scoping (`tenantId, createdAt`)
  - common filters + sort (`status, createdAt DESC` — in Prisma you express this as `@@index([status, createdAt])` and query with `orderBy`).
- Use unique constraints for natural keys:
  - `(tenantId, slug)` or `(workspaceId, email)`.

## Migration-safe modeling
When evolving schemas:
- Add columns as nullable first, backfill, then make required.
- Avoid dropping/renaming columns without data migration.
- Prefer `@map`/`@@map` when renaming in Prisma but keeping DB column stable.
- Never “edit” deployed migrations—create new migrations.

## Output format
When asked to model something, respond with:
1. **Updated Prisma schema snippets** (models/enums).
2. **Rationale** (constraints, relations, indexes).
3. **Migration plan** (steps if change is non-trivial).

## Examples

### Example: 1:N + tenant scoping + indexes
```prisma
model Workspace {
  id        String   @id @default(cuid())
  name      String
  createdAt DateTime @default(now())
  updatedAt DateTime @updatedAt

  projects  Project[]
}

model Project {
  id          String    @id @default(cuid())
  workspaceId String
  name        String
  slug        String
  createdAt   DateTime  @default(now())
  updatedAt   DateTime  @updatedAt

  workspace   Workspace @relation(fields: [workspaceId], references: [id], onDelete: Cascade)

  @@unique([workspaceId, slug])
  @@index([workspaceId, createdAt])
  @@index([workspaceId])
}
```

### Example: explicit M:N join model with metadata
```prisma
model User {
  id    String @id @default(cuid())
  email String @unique

  projectMembers ProjectMember[]
}

model Project {
  id            String @id @default(cuid())
  name          String
  projectMembers ProjectMember[]
}

model ProjectMember {
  projectId String
  userId    String
  role      String
  joinedAt  DateTime @default(now())

  project Project @relation(fields: [projectId], references: [id], onDelete: Cascade)
  user    User    @relation(fields: [userId], references: [id], onDelete: Cascade)

  @@id([projectId, userId])
  @@index([userId])
}
```

## Additional resources

- For complete Prisma docs details, see [reference.md](@.claude/skills/prisma/reference.md)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/madsnyl) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
