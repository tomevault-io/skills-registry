---
name: postgres-migrations
description: Database migration playbook/checklist for schema changes and sqlc query generation/maintenance in Go projects. Use when this capability is needed.
metadata:
  author: mccune1224
---

## What I do
- Summarize steps for creating .up.sql/.down.sql migrations, using golang-migrate
- Provide guidance for keeping sqlc queries (queries.sql) in sync with DB state
- Highlight test patterns for migrations to avoid accidental destructive changes

## When to use me
Use when growing/changing your schema or when generating new go types with sqlc. Ensures teamwide discipline and repeatability in DB evolution.

## Example Usage
Reference when editing files in `backend/internal/db/migrations/` and regenerating code with sqlc after migration deployment.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mccune1224) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
