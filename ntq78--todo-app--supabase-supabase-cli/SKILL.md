---
name: supabase-supabase-cli
description: Use when running Supabase CLI commands or investigating local/production database operations
metadata:
  author: ntq78
---

# Supabase CLI

Reference for Supabase CLI commands for development, testing, and deployment.

## Critical Rules

1. **Check pnpm scripts first** - Use `general-pnpm-scripts` skill to verify wrappers exist
2. **Use --local flag** for local development to prevent accidental production changes
3. **Regenerate types after schema changes** - `pnpm sb:dev:types`

## Quick Reference

### Local Development

| Command               | Purpose                |
| --------------------- | ---------------------- |
| `pnpm sb:dev:start`   | Start local stack      |
| `pnpm sb:dev:stop`    | Stop local stack       |
| `npx supabase status` | Check running services |

### Migrations

| Command                           | Purpose                           |
| --------------------------------- | --------------------------------- |
| `pnpm sb:dev:new <name>`          | Create empty migration            |
| `pnpm sb:dev:diff <name>`         | Generate from UI changes          |
| `pnpm sb:dev:push`                | Apply migrations (preserves data) |
| `pnpm sb:dev:reset`               | Reset + re-run all migrations     |
| `supabase migration list --local` | List local migrations             |

### Database

| Command                      | Purpose                           |
| ---------------------------- | --------------------------------- |
| `supabase status`            | Get Database URL and service info |
| `supabase db lint --local`   | Lint schema for issues            |
| `supabase db push --dry-run` | Preview production push           |
| `pnpm sb:prod:push`          | Push to production                |

### Direct Database Access

Use `supabase status` to get the Database URL for direct psql access:

```bash
# Get connection info
supabase status
# Output includes: Database URL: postgresql://postgres:postgres@127.0.0.1:54322/postgres

# Run SQL directly
psql "postgresql://postgres:postgres@127.0.0.1:54322/postgres" -c "SELECT * FROM my_table"

# Complex queries with INSERT/UPDATE
psql "postgresql://postgres:postgres@127.0.0.1:54322/postgres" -c "
INSERT INTO table_name (col1, col2)
SELECT col1, col2 FROM source_table WHERE condition
RETURNING id;
"
```

**When to use direct psql:**

- Complex INSERT/SELECT operations
- Data migration between tables
- Bulk operations
- When REST API is cumbersome

### Types

| Command             | Purpose                      |
| ------------------- | ---------------------------- |
| `pnpm sb:dev:types` | Generate TS types from local |

### Edge Functions

| Command                                          | Purpose              |
| ------------------------------------------------ | -------------------- |
| `supabase functions serve --env-file .env.local` | Serve locally        |
| `supabase functions deploy <name>`               | Deploy to production |

## Environment Flags

| Flag                  | Target           | Use Case              |
| --------------------- | ---------------- | --------------------- |
| `--local`             | Local stack      | Development, testing  |
| `--linked`            | Production       | Production operations |
| `--project-ref <ref>` | Specific project | Explicit selection    |

## Common Workflows

### Initial Setup

```bash
pnpm sb:dev:start && supabase migration up --local && pnpm sb:dev:types
```

### New Migration

```bash
pnpm sb:dev:new add_feature  # Create
# Edit migration file
pnpm sb:dev:push && pnpm sb:dev:types  # Apply + types
```

### Production Deploy

```bash
pnpm sb:dev:reset  # Verify from scratch
supabase db lint --local  # Check for issues
supabase db push --dry-run  # Preview
pnpm sb:prod:push  # Deploy
```

## Troubleshooting

**Services won't start (port conflict):**

```bash
supabase stop && supabase start
```

**Types out of sync:**

```bash
pnpm sb:dev:types
```

**Migrations out of sync:**

```bash
supabase migration list --local
supabase migration list --linked
pnpm sb:dev:reset  # If corrupted
```

## Local Services Ports

- Database: 54322
- Studio: 54323
- API Gateway: 54321
- Auth: 54324
- Realtime: 54325
- Storage: 54326

## See Also

- **supabase-migrations** - Migration workflow patterns
- **supabase-verification** - Verify schema changes
- **general-pnpm-scripts** - Check for pnpm wrappers first

<!-- Last compacted: 2026-01-15 -->

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ntq78) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
