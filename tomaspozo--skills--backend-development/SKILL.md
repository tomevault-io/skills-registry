---
name: backend-development
description: Supabase backend development workflow. Use for ANY backend work in Supabase projects — schema changes, API endpoints, database functions, RLS policies, edge functions, auth, storage, business logic, or data access. Activate whenever the task involves server-side logic, data layer, or Supabase features. Use when this capability is needed.
metadata:
  author: tomaspozo
---

# Supabase Local Dev Workflow

## Core Philosophy

1. **Schema-driven development** — all structural changes go to schema files, never direct SQL
2. **RPC-first architecture** — no direct `supabase-js` table calls; all data access through RPCs
3. **DB functions as first-class citizens** — business logic lives in the database

---

## Process

### Phase 0: Setup Verification (run once per project)

Before starting any backend work, verify the project's infrastructure is in place.

**1. Run the check query** — Load [`assets/check_setup.sql`](./assets/check_setup.sql) and execute it via `execute_sql`. It returns a JSON object like:

```json
{
  "extensions": { "pg_net": true, "vault": true },
  "functions":  { "_internal_get_secret": true, "_internal_call_edge_function": true, "_internal_call_edge_function_sync": true },
  "secrets":    { "SUPABASE_URL": true, "SB_PUBLISHABLE_KEY": true, "SB_SECRET_KEY": true },
  "ready": true
}
```

If `"ready": true` — skip to Phase 1. Otherwise, fix what's missing:

**2. Missing extensions** — Apply via `apply_migration`:

```sql
CREATE EXTENSION IF NOT EXISTS pg_net;
-- vault is typically enabled by default; if not:
CREATE EXTENSION IF NOT EXISTS supabase_vault;
```

**3. Missing internal functions** — Copy [`assets/setup.sql`](./assets/setup.sql) functions into the project's `supabase/schemas/50_functions/_internal/` schema files, then apply via `apply_migration`.

**4. Missing Vault secrets** — See [`assets/seed.sql`](./assets/seed.sql) for the full template and explanation of why these secrets are needed. Store secrets via `execute_sql` (`SELECT vault.create_secret('<value>', '<secret_name>')`) or the fallback script (`./scripts/setup_vault_secrets.sh`). Required names: `SUPABASE_URL`, `SB_PUBLISHABLE_KEY`, `SB_SECRET_KEY`.

**5. Persist secrets for `db reset`** — Vault secrets are wiped on every `supabase db reset`. Append the vault secret SQL from [`assets/seed.sql`](./assets/seed.sql) (with the user's actual local values) to the project's `supabase/seed.sql` so they are repopulated automatically. The file may already contain other seed data — append, don't overwrite.

**6. Re-run the check** to confirm `"ready": true` before proceeding.

> **📝 Load [Initial Project Setup](./references/workflows.md#initial-project-setup) for the detailed step-by-step workflow.**

---

### Phase 1: Schema Changes

Write structural changes to the appropriate schema file based on the folder structure:

```
supabase/schemas/
├── 10_types/        # Enums, composite types, domains
├── 20_tables/       # Table definitions
├── 30_constraints/  # Check constraints, foreign keys
├── 40_indexes/      # Index definitions
├── 50_functions/    # RPCs, auth functions, internal utils
│   ├── _internal/   # Infrastructure utilities
│   └── _auth/       # RLS policy functions
├── 60_triggers/     # Trigger definitions
├── 70_policies/     # RLS policies
└── 80_views/        # View definitions
```

Files are organized by entity (e.g., `charts.sql`, `readings.sql`). Numeric prefixes ensure correct application order.

**📋 Load [Naming Conventions](./references/naming_conventions.md) for table, column, and function naming rules.**

### Phase 2: Apply & Fix

1. CLI auto-applies changes (`supabase start`)
2. Monitor logs for errors (constraint violations, dependencies)
3. If errors → use `execute_sql` MCP tool for data fixes only (UPDATE, DELETE, INSERT)
4. Never use `execute_sql` for schema structure — only schema files

### Phase 3: Generate Types

```bash
supabase gen types typescript --local > src/types/database.ts
```

### Phase 4: Iterate

Repeat Phases 1-3 until schema is stable and tested.

### Phase 5: Migration

1. Use `supabase db diff` to generate migration
2. Review migration — patch if manual SQL commands are missing

---

## Reference Files

Load these as needed during development:

### Conventions & Patterns

- **[📋 Naming Conventions](./references/naming_conventions.md)** — Tables, columns, functions, indexes
- **[🔐 RPC Patterns](./references/rpc_patterns.md)** — RPC-first architecture, auth functions, RLS policies
- **[⚡ Edge Functions](./references/edge_functions.md)** — Project structure, shared utilities, CORS, error helpers
- **[🔧 withSupabase Wrapper](./references/with_supabase.md)** — Wrapper rules, allow selection, client usage patterns

### Setup & Infrastructure

- **[🔍 Setup Check](./assets/check_setup.sql)** — Verify extensions, functions, and secrets exist
- **[⚙️ Setup Guide](./assets/setup.sql)** — Internal utility function definitions
- **[🌱 Seed Template](./assets/seed.sql)** — Vault secrets for local dev (append to `supabase/seed.sql`)
- **[🔐 Vault Secrets Script](./scripts/setup_vault_secrets.sh)** — Store secrets in Vault (manual fallback)

### Workflows

- **[📝 Common Workflows](./references/workflows.md)** — Adding entities, fields, creating RPCs

### Entity Tracking

- **[📊 Entity Registry Template](./references/ENTITIES.md)** — Track entities and schema files

---

## Tools & Dependencies

| Tool           | Purpose                                                                                                       |
| -------------- | ------------------------------------------------------------------------------------------------------------- |
| Supabase CLI   | Local development, type generation, migrations                                                                |
| Supabase MCP   | `execute_sql` tool for data fixes                                                                             |
| Edge Functions | See [Edge Functions](./references/edge_functions.md) for project structure and [withSupabase](./references/with_supabase.md) for wrapper usage |

---

## Quick Reference

**Client-side rule** — Never direct table access:

```typescript
// ❌ WRONG
const { data } = await supabase.from("charts").select("*");

// ✅ CORRECT
const { data } = await supabase.rpc("chart_get_by_user", { p_user_id: userId });
```

**Security context rule** — SECURITY INVOKER by default:

```sql
-- ❌ WRONG — bypasses RLS then reimplements filtering manually
CREATE FUNCTION chart_get_by_id(p_chart_id uuid)
RETURNS jsonb LANGUAGE plpgsql SECURITY DEFINER SET search_path = '' AS $$
BEGIN
  SELECT ... FROM public.charts WHERE id = p_chart_id AND user_id = auth.uid(); -- manual filter = fragile
END; $$;

-- ✅ CORRECT — RLS handles access control automatically
CREATE FUNCTION chart_get_by_id(p_chart_id uuid)
RETURNS jsonb LANGUAGE plpgsql SECURITY INVOKER SET search_path = '' AS $$
BEGIN
  SELECT ... FROM public.charts WHERE id = p_chart_id; -- RLS enforces permissions
END; $$;
```

**When to use SECURITY DEFINER (rare exceptions):**

- `_auth_*` functions called by RLS policies (they run during policy evaluation, need to bypass RLS to query the table they protect)
- `_internal_*` utility functions that need elevated access (e.g., reading vault secrets)
- Multi-table operations that need cross-table access the user's role can't reach
- Always document WHY with a comment: `-- SECURITY DEFINER: required because ...`

**Function prefixes:**

- Business logic: `{entity}_{action}` → `chart_create` (SECURITY INVOKER)
- Auth (RLS): `_auth_{entity}_{check}` → `_auth_chart_can_read` (SECURITY DEFINER — needed by RLS)
- Internal: `_internal_{name}` → `_internal_get_secret` (SECURITY DEFINER — elevated access)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/tomaspozo) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
