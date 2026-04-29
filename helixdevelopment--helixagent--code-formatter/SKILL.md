---
name: code-formatter
description: | Use when this capability is needed.
metadata:
  author: helixdevelopment
---

# Code Formatter

## Overview

This skill provides automated assistance for the described functionality.

## Prerequisites

- Node.js and npm/npx installed
- Prettier available globally or locally
- Write permissions for target files
- Supported file types in the project

## Instructions

1. Analyze current formatting (`prettier --check`) and identify files to update.
2. Configure formatting rules (`.prettierrc`, `.editorconfig`) for the project.
3. Apply formatting (`prettier --write`) to the target files/directories.
4. Add ignore patterns (`.prettierignore`) for generated/vendor outputs.
5. Optionally enforce formatting via git hooks (husky/lint-staged).


See `{baseDir}/references/implementation.md` for detailed implementation guide.

## Output



## Error Handling

See `{baseDir}/references/errors.md` for comprehensive error handling.

## Examples

See `{baseDir}/references/examples.md` for detailed examples.

## Resources

- name: Check formatting
- name: Enforce formatting
- **ESLint** - Linting and code quality
- **Stylelint** - CSS/SCSS linting
- **Markdownlint** - Markdown style checking

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/helixdevelopment) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
