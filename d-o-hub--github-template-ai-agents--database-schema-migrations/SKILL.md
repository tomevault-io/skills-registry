---
name: database-schema-migrations
description: > Use when this capability is needed.
metadata:
  author: d-o-hub
---

# Database Schema Migrations

Manage schema design, migrations, and database operations.

## Key Responsibilities
- Design and version schema changes.
- Write migration scripts.
- Ensure database compatibility.
- Provide rollback-safe migration steps.

## Migration Pattern

```sql
-- Good: Idempotent migration
CREATE TABLE IF NOT EXISTS resources (
  id TEXT PRIMARY KEY,
  title TEXT NOT NULL,
  owner_id TEXT NOT NULL,
  created_at INTEGER DEFAULT (unixepoch())
);

-- Good: Safe index addition
CREATE INDEX IF NOT EXISTS idx_resources_owner ON resources(owner_id);
```

## Naming Convention
- Migration files: `YYYYMMDD_description.sql`
- Tables: snake_case, plural (e.g., `resources`, `permissions`)
- Columns: snake_case (e.g., `resource_id`, `created_at`)

## Constraints
- Migrations must be idempotent and reversible.
- Indexes must be added in a way that doesn't break existing queries.
- Use timestamp-prefixed filenames.
- Always provide rollback scripts.

## Checklist
- [ ] Migration is idempotent (can run multiple times safely).
- [ ] Rollback migration tested.
- [ ] Indexes use `IF NOT EXISTS`.
- [ ] Foreign keys defined where applicable.
- [ ] Migration logged in migration history table.

## References
- `references/migration-patterns.md` - Common migration patterns by database type
- `references/rollback-strategies.md` - Safe rollback strategies

---
> Source: [d-o-hub/github-template-ai-agents](https://github.com/d-o-hub/github-template-ai-agents) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-22 -->
