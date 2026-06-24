---
name: db-migration
description: Create and apply Prisma migrations; validate schema. Use when changing the database schema, adding models or columns, or running migrations. Use when this capability is needed.
metadata:
  author: sedarged
---

# DB Migration

Create and apply Prisma migrations for TikTok-AI-Agent.

## Input

- Description of the change (e.g. "add Project.costCents") or concrete diff. Optionally a migration name.

## Steps

1. **Edit schema** – Update `apps/server/prisma/schema.prisma`. Keep relations, `@id`, `@default`, etc. consistent.
2. **Create migration** – From repo root:
   - `npm run db:migrate:dev` (creates and applies in dev), or
   - `cd apps/server && npx prisma migrate dev --name <snake_case_name>`.
3. **Verify** – Run `npx prisma generate` (or `npm run db:generate`) and ensure the app typechecks. Run `npm run test` if touching code that uses the new schema.
4. **Report** – "Migration created and applied" or "Migration failed: …" with next steps (e.g. fix schema, resolve conflicts).

## Output

- Updated `schema.prisma` and a new migration under `apps/server/prisma/migrations/`.
- Short summary: success or failure and what to do next.

## References

- [apps/server/prisma/schema.prisma](apps/server/prisma/schema.prisma)
- [AGENTS.md](AGENTS.md) – `db:generate`, `db:migrate:dev`
- [.cursor/rules/always-project-standards.mdc](.cursor/rules/always-project-standards.mdc) – DB section

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/sedarged) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
