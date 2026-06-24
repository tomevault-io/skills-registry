---
name: deploy-verify
description: Deploy with deterministic preflight, verification, and optional rollback automation using the configured deploy adapter. Use when this capability is needed.
metadata:
  author: bishnubista
---

# /stacklane:deploy-verify

Use the script entrypoint for release tracking and rollback-safe behavior.

## Command

```bash
bash "${CLAUDE_PLUGIN_ROOT}/scripts/stacklane-deploy-verify.sh" --environment staging
```

## Optional Flags

- `--environment <staging|production>`
- `--service <name>`
- `--healthcheck-url <url>`
- `--smoke-command "..."`
- `--auto-rollback`
- `--rollback-target <deployment-id>`
- `--confirm-production I_UNDERSTAND_PRODUCTION` (required for production)
- `--dry-run`

## Execution Rules

1. Require a healthcheck URL (`--healthcheck-url` or `.stacklane.json healthcheck.url`).
2. Healthcheck URL must be an absolute `http://` or `https://` URL.
3. For production, require `--confirm-production I_UNDERSTAND_PRODUCTION`.
4. If verification fails:
   - without `--auto-rollback`: report blocked release and rollback command
   - with `--auto-rollback`: execute rollback path and report outcome
5. Auto-rollback requires a configured `commands.rollback` template.
6. Return release report path in `.stacklane/releases/` and final status (`deployed`, `rolled-back`, or failure).

## Boundaries

- Do not skip preflight validations.
- Do not invent rollback commands outside configured templates.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/bishnubista) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
