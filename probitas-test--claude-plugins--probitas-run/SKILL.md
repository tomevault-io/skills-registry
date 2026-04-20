---
name: probitas-run
description: Running and validating Probitas scenarios. Use when executing tests, running scenarios, or debugging test failures. Use when this capability is needed.
metadata:
  author: probitas-test
---

## Instructions

Run `/probitas-run` command (or `/probitas-run <selector>` with filter).

If project not initialized → run `/probitas-init` first.

## Selector Examples

```bash
probitas run -s tag:api             # Match tag
probitas run -s "!tag:slow"         # Exclude tag
probitas run -s user                # Match scenario name
```

## If Tests Fail

1. Run `/probitas-check` for validation errors
2. Check error messages for assertion details
3. Verify environment variables (API_URL, DATABASE_URL)
4. Use `--verbose` or `--debug` for more output

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/probitas-test) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
