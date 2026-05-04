---
name: compose-security-lint
description: Lint Docker Compose files for Makefile-first deployment environments using .deploy.env.common + .deploy.env.<ENV_MODE>. Use when validating local/test/prod/custom compose files for image/build declaration, restart, healthcheck, logging, and external network settings. Use when this capability is needed.
metadata:
  author: neversight
---

# Compose Security Lint

1. Resolve compose file from `.deploy.env.common` + `.deploy.env.<ENV_MODE>` or lint all inferred compose files.
2. Validate safety baseline fields.
3. Enforce extra production checks (`deploy.resources`).

## Command
```bash
python3 skills/compose-security-lint/scripts/lint_compose.py --root . --env-mode prod
python3 skills/compose-security-lint/scripts/lint_compose.py --root . --all
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/neversight) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
