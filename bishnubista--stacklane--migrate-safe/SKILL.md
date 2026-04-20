---
name: migrate-safe
description: Apply Supabase migrations through a guarded, script-backed workflow with rollback notes and verification. Use when this capability is needed.
metadata:
  author: bishnubista
---

# /stacklane:migrate-safe

Use the script entrypoint for deterministic safety checks and report artifacts.

## Command

```bash
bash "${CLAUDE_PLUGIN_ROOT}/scripts/stacklane-migrate-safe.sh" --environment staging
```

## Optional Flags

- `--environment <staging|production>`
- `--service <name>`
- `--confirm-production I_UNDERSTAND_PRODUCTION` (required for production)
- `--smoke-command "..."`
- `--skip-verify`
- `--dry-run`

## Execution Rules

1. Parse user arguments and map to script flags.
2. Never apply to production without `--confirm-production I_UNDERSTAND_PRODUCTION`.
3. This command currently requires `database=supabase` in the active profile.
4. Do not bypass script preflight checks.
5. On completion, return paths to:
   - rollback notes in `.stacklane/migration-reports/`
   - apply report in `.stacklane/migration-reports/`
6. If `commands.rollback` is not configured, return rollback guidance as "not configured" rather than inventing a command.

## Boundaries

- Migration scope only. No deploy in this command.
- No unrelated app refactors.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/bishnubista) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
