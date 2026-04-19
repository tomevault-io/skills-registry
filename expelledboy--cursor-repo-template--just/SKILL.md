---
name: justfile-mastery
description: Use when you need to author, edit, debug, or run a justfile, including modules, parameters, and advanced features.
metadata:
  author: expelledboy
---

# Purpose
Create, edit, and operate `justfile` recipes safely and predictably.

# Scope
Covers justfile syntax, execution rules, parameters, substitutions, attributes, modules, and common CLI discovery flags. It does not cover installing `just` or repository-specific policies beyond linked sources.

# Minimal Path
1) Inspect current recipes: `just --list` and `just --show <recipe>`.
2) Author or edit a recipe using standard syntax and grouping attributes.
3) Validate parameters and quoting, especially for arguments that may contain spaces.
4) Run the recipe and verify dependencies, working directory, and execution mode.
5) If complexity grows, split into modules and update module invocation.

# Validation
- `just --list` shows expected recipes and groups.
- `just --show <recipe>` reflects intended commands.
- Running the recipe succeeds with expected output and side effects.

# Failure Modes
- Parameters split unexpectedly due to missing quotes.
- Recipes rely on shell state across lines without using `[script]`.
- Module recipes fail due to working directory assumptions.

# References
- Context7 MCP: just manual (`just_systems_man_en`)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/expelledboy) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
