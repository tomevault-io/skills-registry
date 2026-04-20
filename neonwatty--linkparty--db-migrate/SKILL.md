---
name: db-migrate
description: Scaffold a new Supabase migration file with the correct naming convention. Usage: /db-migrate <description> Use when this capability is needed.
metadata:
  author: neonwatty
---

Create a new Supabase database migration for: $ARGUMENTS

## Rules

1. NEVER edit existing migration files in `supabase/migrations/` — always create a new one
2. Read the existing migrations to understand the current schema before writing SQL

## Steps

1. List files in `supabase/migrations/` to determine the next sequence number (format: `NNN`)
2. Generate a filename: `supabase/migrations/NNN_<description>.sql` where:
   - `NNN` is the next zero-padded 3-digit number (e.g., `010`, `011`)
   - `<description>` is derived from `$ARGUMENTS`, lowercased, spaces replaced with underscores, alphanumeric only
3. Create the migration file with this template:

```sql
-- Migration: NNN_<description>
-- Purpose: <one-line summary from $ARGUMENTS>
--
-- To apply: supabase db push
-- To revert: <describe manual rollback steps>

-- Add your SQL here
```

4. Fill in the SQL based on the description in `$ARGUMENTS`
5. If the migration adds a table, include RLS policies (this project enforces RLS on all tables)
6. If the migration adds columns, use `ALTER TABLE ... ADD COLUMN` with appropriate defaults
7. After creating the file, show the user the full SQL and ask them to review before applying

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/neonwatty) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
