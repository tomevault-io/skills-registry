---
name: gorm-sql-migration-safety
description: Database migration safety review for GORM + PostgreSQL. Use when writing or reviewing schema changes, AutoMigrate usage, ALTER TABLE statements, or index creation. Covers zero-downtime patterns, NOT NULL backfill, CONCURRENTLY indexes, idempotency, and rollback files. Use when this capability is needed.
metadata:
  author: saifoelloh
---

# Migration Safety

Expert-level review of database migration safety for production PostgreSQL deployments. Ensures zero-downtime schema changes, correct rollback capability, and idempotent migrations.

## When to Apply

Use this skill when:
- Writing new migration files (`*.up.sql`, `*.down.sql`)
- Using GORM `AutoMigrate`
- Adding columns, indexes, or constraints to existing tables
- Dropping columns or tables
- Reviewing migrations before deploying to production

## Zero-Downtime Migration Checklist

Before any production migration:
- [ ] NOT NULL column has DEFAULT or backfill migration first
- [ ] Indexes use `CONCURRENTLY`
- [ ] Migration is idempotent (`IF NOT EXISTS` / `IF EXISTS`)
- [ ] `down.sql` rollback file exists and is tested
- [ ] DROP COLUMN is in a separate deploy after code references removed
- [ ] AutoMigrate is not in the application startup path

## Rule Categories

| Priority | Count | Focus |
|---|---|---|
| Critical | 3 | AutoMigrate in production, NOT NULL backfill, missing rollback |
| High | 2 | CONCURRENTLY indexes, idempotent migrations |
| Medium | 1 | DROP COLUMN deprecation period |

## Rules Covered (6 total)

### Critical Issues (3)
- `critical-no-automigrate-production` — Never run AutoMigrate in production startup path
- `critical-not-null-backfill` — NOT NULL column addition requires 3-step migration
- `critical-always-provide-rollback` — Every migration must have a paired `down.sql`

### High-Impact Patterns (2)
- `high-create-index-concurrently` — Use `CREATE INDEX CONCURRENTLY` to avoid write locks
- `high-idempotent-migration` — Use `IF NOT EXISTS` / `IF EXISTS` for safe re-runs

### Medium Improvements (1)
- `medium-drop-column-deprecation` — Deprecate before dropping — never drop in same deploy as code changes

## Trigger Phrases

- "Review this migration"
- "Is this migration safe?"
- "ALTER TABLE"
- "ADD COLUMN / DROP COLUMN"
- "CREATE INDEX"
- "AutoMigrate"
- "Schema change"
- "Zero-downtime migration"

## Related Skills

- [query-performance](../query-performance/SKILL.md) — For index design (partial, composite)
- [postgresql-syntax](../postgresql-syntax/SKILL.md) — For correct DDL SQL syntax

---
> Source: [saifoelloh/golang-best-practices-skill](https://github.com/saifoelloh/golang-best-practices-skill) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
