---
name: makefile-contract-lint
description: Lint Makefile contract for common+env override deployment workflow. Use when validating deployment variables, include rules, remote port handling, and required targets. Use when this capability is needed.
metadata:
  author: neversight
---

# Makefile Contract Lint

1. Validate deployment markers and required variables.
2. Validate common+env include strategy.
3. Validate required Makefile targets.
4. Validate `FULL_REGISTRY_IMAGE` composition and `ssh/scp` port usage.

## Command
```bash
python3 skills/makefile-contract-lint/scripts/lint_makefile.py --root .
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/neversight) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
