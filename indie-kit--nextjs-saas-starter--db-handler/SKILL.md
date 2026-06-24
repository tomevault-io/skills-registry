---
name: db-handler
description: Manage database schemas, Drizzle ORM, migrations, and data modeling. Use when this capability is needed.
metadata:
  author: indie-kit
---

# DB Handler

## Instructions
- Use `npm run db:generate` to generate migrations after schema changes.
- Use `npm run db:migrate` to apply migrations.
- Use `drizzle-orm` for queries (e.g., `eq`, `and`, `or`).
- Schema files are located in `src/db/schema/`.
- **IMPORTANT**: All schema files must be exported from `src/db/schema/index.ts`.
- Connection string is `DATABASE_URL`.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/indie-kit) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
