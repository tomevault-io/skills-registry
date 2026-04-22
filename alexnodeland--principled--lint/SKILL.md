---
name: lint
description: > Use when this capability is needed.
metadata:
  author: alexnodeland
---

# Lint — Full Quality Suite

Run the full lint suite for this repository and report results.

## Command

```
/lint
```

## Workflow

1. **Shell formatting check** — run `shfmt -i 2 -bn -sr -d` on all `.sh` files under `plugins/` and any other directories. Report any files that need formatting.

2. **Shell lint** — run `shellcheck --shell=bash` on all `.sh` files under `plugins/` and any other directories. Report any warnings or errors.

3. **Markdown lint** — run `npx markdownlint-cli2 '**/*.md'`. Report any rule violations.

4. **Markdown formatting check** — run `npx prettier --check '**/*.md'`. Report any files that need formatting.

5. **Summary** — report the total number of errors per tool and list all affected files. If everything passes, confirm the repo is lint-clean.

If any tool reports errors, suggest the fix command (e.g., `shfmt -w`, `prettier --write`).

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/alexnodeland) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
