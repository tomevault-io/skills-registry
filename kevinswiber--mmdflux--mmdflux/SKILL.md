---
name: verify
description: Run full project verification (lint + test + architecture boundaries) Use when this capability is needed.
metadata:
  author: kevinswiber
---

Run the full project verification suite:

```bash
just check
```

This runs three stages in sequence:

1. **Lint** — `cargo +nightly fmt -- --check` and clippy
2. **Test** — `cargo nextest run` (all tests, parallel)
3. **Architecture** — `cargo xtask architecture` (semantic boundary enforcement)

If any stage fails, report the failure clearly and fix it before re-running. Do not skip stages.

If only a specific stage needs re-checking after a fix, run it individually:

- `just lint` — format + clippy only
- `just test` — tests only
- `just architecture-check` — check architecture boundaries only

---
> Source: [kevinswiber/mmdflux](https://github.com/kevinswiber/mmdflux) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-18 -->
