---
name: deployment-version-policy
description: Normalize and validate deployment version under Makefile-first workflow. Use when reading or validating version for test/prod/custom environments before make-based deployment. Use when this capability is needed.
metadata:
  author: neversight
---

# Deployment Version Policy

1. Resolve version from CLI or Makefile default.
2. Validate version format.
3. Optionally disallow `latest` for prod.

## Command
```bash
python3 skills/deployment-version-policy/scripts/check_version.py --root . --env-mode prod
python3 skills/deployment-version-policy/scripts/check_version.py --root . --env-mode prod --disallow-latest-prod
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/neversight) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
