---
name: lint-build-test
description: How to check code by linting, building, and testing. Use when this capability is needed.
metadata:
  author: metamask
---

When asked to check, lint, build, or test code, follow these steps:

## 1. Analyze changed files

First, check which files have changed using `git status` or `git diff --name-only`.

Categorize the changes:

- **Source files**: `.ts`, `.js`, `.mts`, `.mjs`, `.cjs`, `.cts`, `.tsx`, `.jsx`
- **Meta files**: `.md`, `.yml`, `.yaml`, `.json`, `.html`

## 2. Determine what to run

Based on the changed files:

- **No files changed**: Nothing to do.
- **Only meta files changed**: Run only `yarn lint:misc --write` (or `yarn workspace <package-name> lint:misc --write` for a specific package).
- **Source files changed**: Run the full check (see below).

## 3. Run the full check (if needed)

### For a specific package

If a package name is specified (e.g. `@metamask/ocap-kernel`):

1. `yarn workspace <package-name> lint:fix`
2. `yarn workspace <package-name> build`
3. `yarn workspace <package-name> test:dev:quiet`

### For the entire monorepo

If no package is specified:

1. `yarn lint:fix`
2. `yarn build`
3. `yarn test:dev:quiet`

Report any errors encountered during these steps.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/metamask) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
