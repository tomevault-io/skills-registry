---
name: init
description: Initialize stacklane baseline using deterministic scripts and optional adapter profiles (nextjs/tanstack, railway/flyio). Use when this capability is needed.
metadata:
  author: bishnubista
---

# /stacklane:init

Use the script entrypoint, not ad-hoc shell steps.

## Command

```bash
bash "${CLAUDE_PLUGIN_ROOT}/scripts/stacklane-init.sh"
```

## Optional Flags

- `--runtime <bun>`
- `--frontend <nextjs|tanstack|none>`
- `--database <supabase|none>`
- `--deploy <railway|flyio|none>`
- `--service <name>`
- `--environment <name>`
- `--healthcheck-url <url>`
- `--no-validate`
- `--dry-run`

## Execution Rules

1. Parse any user-provided options and pass them directly to the script.
2. Run from the project root.
3. If the script fails, stop and return the exact failing stage.
4. On success, summarize:
   - `.stacklane/init-report.md`
   - whether `.stacklane.json` was created or reused
   - active adapter profile and marker readiness

## Boundaries

- Do not deploy.
- Do not apply migrations.
- Do not replace script behavior with custom manual steps.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/bishnubista) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
