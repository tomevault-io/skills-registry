---
name: deployment-env-isolation-check
description: Validate environment isolation using merged values from .deploy.env.common and .deploy.env.<ENV_MODE>. Use when ensuring test/prod/custom do not share the same registry, remote host, user, and port identity. Use when this capability is needed.
metadata:
  author: neversight
---

# Deployment Env Isolation Check

1. Read `.deploy.env.common` and all `.deploy.env.<env>` files.
2. Merge common defaults with per-env overrides.
3. Compare environment identities (`registry + user + host + port`).
4. Fail when non-prod and prod identities are identical.

## Command
```bash
python3 skills/deployment-env-isolation-check/scripts/check_isolation.py --root .
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/neversight) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
