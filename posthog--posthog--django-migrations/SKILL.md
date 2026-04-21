---
name: django-migrations
description: Django migration patterns and safety workflow for PostHog. Use when creating, adjusting, or reviewing Django/Postgres migrations, including non-blocking index/constraint changes, multi-phase schema changes, data backfills, migration conflict rebasing, and product model moves that require SeparateDatabaseAndState. Use when this capability is needed.
metadata:
  author: PostHog
---

# Django migrations

Read these files first, before writing or editing a migration:

- `docs/published/handbook/engineering/developing-locally.md` (`## Django migrations`, `### Non-blocking migrations`, `### Resolving merge conflicts`)
- `docs/published/handbook/engineering/safe-django-migrations.md`
- `docs/published/handbook/engineering/databases/schema-changes.md`
- `products/README.md` (`## Adding or moving backend models and migrations`) when working in `products/*`

If the task is a ClickHouse migration, use `clickhouse-migrations` instead.

## Workflow

1. **Classify** the change as additive (new nullable column, new table) or risky (drop/rename, `NOT NULL`, indexes, constraints, large data updates, model moves).
2. **Generate**: `DEBUG=1 ./manage.py makemigrations [app_label]`.
   For merge conflicts: `python manage.py rebase_migration <app> && git add <app>/migrations` (`posthog` or `ee`).
3. **Apply safety rules** from `safe-django-migrations.md` — the doc covers multi-phase rollouts, `SeparateDatabaseAndState`, concurrent operations, idempotency, and all risky patterns in detail.
4. **Validate**: `./manage.py sqlmigrate <app> <migration_number>`, run tests, confirm linear migration sequence.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/PostHog) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
