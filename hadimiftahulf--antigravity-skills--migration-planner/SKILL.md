---
name: migration-planner
description: Plans safe database migrations with rollback strategies, data integrity checks, and zero-downtime deployment considerations. Use when this capability is needed.
metadata:
  author: hadimiftahulf
---
# Migration Planner (The Strategist 🗺️)

"Migrations are surgery on a living patient."

## When to Activate
- User mentions: "migration", "alter table", "add column", "schema change", "database update".
- Any task involving structural changes to the database.

## Workflow
1.  **Impact Analysis**: What tables/columns are affected? What data exists?
2.  **Risk Assessment**:
    - Is data loss possible? (column drop, type change)
    - Is downtime required? (large table lock)
    - Are there foreign key dependencies?
3.  **Migration Plan**:
    - Write migration UP and DOWN (rollback).
    - If destructive: require explicit user confirmation.
    - If large table: suggest batched migration or online DDL.
4.  **Safety Checklist**:
    - [ ] Backup strategy defined?
    - [ ] Rollback tested?
    - [ ] Foreign keys handled?
    - [ ] Indexes considered?
    - [ ] Seeder/data migration needed?

## Rules
- NEVER drop a column without explicit user approval.
- ALWAYS provide a rollback migration.
- For production: suggest blue-green or phased deployment.

## Cost: Medium

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/hadimiftahulf) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
