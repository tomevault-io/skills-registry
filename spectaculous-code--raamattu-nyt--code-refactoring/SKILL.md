---
name: code-refactoring
description: Expert refactoring orchestrator for large-scale code changes with change tracking. Use when (1) renaming/moving files or directories, (2) restructuring database schemas, (3) refactoring APIs or Edge Functions, (4) splitting/merging components, (5) querying what changed in a system, (6) migrating IdeaMachina evolution store from Zustand to Supabase, (7) refactoring IdeaMachina components or data layer. Maintains changelog for docs-updater sync. Use when this capability is needed.
metadata:
  author: spectaculous-code
---

# Code Refactoring Wizard

Orchestrate large-scale refactoring with change tracking. Uses `code-wizard` for discovery.

## Context Files

For structure and conventions, read from `Docs/context/`:
- `Docs/context/conventions.md` - Architecture rules, refactor guidelines
- `Docs/context/repo-structure.md` - Where to place files
- `Docs/context/packages-map.md` - Package boundaries

## Changelog Location

**All significant changes logged to:** `Docs/ai/CHANGELOG.md`

This file is monitored by `docs-updater` skill for documentation sync.

## Change Categories

| Category | Tag | Example |
|----------|-----|---------|
| Schema | `[SCHEMA]` | New table, column rename, migration |
| Structure | `[STRUCTURE]` | File/folder move, directory reorganization |
| API | `[API]` | RPC signature change, Edge Function update |
| Breaking | `[BREAKING]` | Removed feature, renamed export |
| Component | `[COMPONENT]` | React component split/merge |
| Dependency | `[DEPS]` | Package upgrade, new import |

## Workflow

### 1. Discover (use code-wizard)

```
/code-wizard "find all usages of ai_plan_quotas table"
/code-wizard "where is subscription logic implemented"
```

### 2. Plan

- Current state → Target state
- Migration path
- Rollback strategy
- Affected files list

### 3. Execute

Order: Database → Backend → Frontend → Tests

### 4. Log to CHANGELOG.md

```markdown
## 2026-01-08 - Subscription System Refactor

### [SCHEMA] Replace per-feature quotas with token pools

**Before:**
- `ai_plan_quotas` table (40+ rows)
- Per-feature token limits

**After:**
- `subscription_plans` table
- `token_pools` table
- `ai_operations` table

**Impact:**
- Files: 15 | Migration: Yes | Breaking: Yes

**Related:** #20260108_token_pool_system
```

### 5. Sync Docs

```
/docs-updater "sync changelog to API docs"
```

## Query Changes

To answer "what changed in X system":

1. Read `Docs/ai/CHANGELOG.md`
2. Filter by date/category/system
3. Summarize

Example response:
```
## Subscription System Changes (Jan 2026)

1. [SCHEMA] Token pool migration (Jan 8)
   - Replaced ai_plan_quotas → unified token system
   - 15 files, breaking

2. [API] New check_token_balance RPC (Jan 8)
   - User token balance endpoint
   - Non-breaking
```

## IdeaMachina Refactoring

For migrating IdeaMachina's evolution store from Zustand (client-side JSON blob) to Supabase relational tables, see the comprehensive guide:

**[references/idea-machina-evolution.md](references/idea-machina-evolution.md)**

Quick summary:
- **Current state:** `projectEvolutionStore` (Zustand) persists evolution data as a JSON blob to `store_sync` table. 30 files depend on it. 10 store versions (9 migrations).
- **Target state:** Relational tables in `ai_prompt` schema (`pm_evolutions`, `pm_sparks`, `pm_cores`, `pm_directions`, `pm_customer_personas`, `pm_force_modules`) with RLS, queried via React Query.
- **Migration strategy:** 3 phases — (1) schema + dual-write, (2) switch reads to DB, (3) remove Zustand store.

Use Pattern 7 in [references/patterns.md](references/patterns.md) for the general Zustand→DB migration pattern.

## Related Skills

| Skill | Use For |
|-------|---------|
| `code-wizard` | Find code before refactoring |
| `docs-updater` | Sync docs after changelog |
| `supabase-migration-writer` | Database migrations |
| `admin-panel-builder` | Admin page refactoring |
| `idea-machina` | IdeaMachina feature context and evolution pipeline |

## References

- **Refactoring patterns**: See [references/patterns.md](references/patterns.md)
- **Changelog examples**: See [references/changelog-examples.md](references/changelog-examples.md)
- **IdeaMachina evolution migration**: See [references/idea-machina-evolution.md](references/idea-machina-evolution.md)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/spectaculous-code) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
