---
name: backend-migrations
description: Create and manage database migrations following best practices for schema changes and data migrations. Use this skill when creating database migration files, modifying database schemas, adding or removing tables and columns, working with migration files (prisma/migrations/*, db/migrate/*, migrations/*), implementing reversible migrations with rollback methods, managing database indexes, handling zero-downtime deployments, separating schema changes from data migrations, or ensuring backwards compatibility during schema updates. Apply this skill when setting up new database tables, altering existing database structures, creating migration scripts, or reviewing migration safety and rollback strategies. Use when this capability is needed.
metadata:
  author: pdovhomilja
---

# Backend Migrations

## When to use this skill

- When creating new database migration files (e.g., `prisma/migrations/*`, `db/migrate/*`, `migrations/*`)
- When modifying database schemas (adding, removing, or altering tables and columns)
- When implementing rollback or down methods for migrations
- When adding or modifying database indexes on large tables
- When separating schema changes from data migrations
- When considering zero-downtime deployment strategies for database changes
- When naming migration files with descriptive, clear names
- When reviewing migration safety and ensuring backwards compatibility
- When managing database constraints (foreign keys, NOT NULL, UNIQUE)
- When planning multi-step migrations for complex schema changes
- When working with version-controlled database schema definitions

This Skill provides Claude Code with specific guidance on how to adhere to coding standards as they relate to how it should handle backend migrations.

## Instructions

For details, refer to the information provided in this file:
[backend migrations](../../../agent-os/standards/backend/migrations.md)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/pdovhomilja) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
