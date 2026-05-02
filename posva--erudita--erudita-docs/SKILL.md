---
name: erudita-docs
description: Fetch and install llms.txt documentation using the erudita CLI. Use when a user asks to download, cache, or install documentation for packages, or to update erudita.json and .erudita links. Use when this capability is needed.
metadata:
  author: posva
---

# Erudita Docs

## Workflow

- Use `erudita install <package...>` to fetch and add documentation for specified packages.
- If no packages are passed, `erudita install` reads `erudita.json` and prunes stale entries.

## Tips

- Prefer `erudita install --deps <dev|prod|all>` to cache docs for project dependencies.
- Use `erudita install --mode <link|copy>` to control whether `.erudita/` uses symlinks or copies.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/posva) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
