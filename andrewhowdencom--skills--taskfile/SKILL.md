---
name: taskfile
description: Guidelines for standard tasks in Taskfile.yml. Use when this capability is needed.
metadata:
  author: andrewhowdencom
---

# Task Runner

## Standard Tasks
[Taskfile](https://taskfile.dev/) is the mandatory task runner. Most projects should implement the following standard tasks in `Taskfile.yml`:

- `generate`: Generates code as required (e.g., from Protocol Buffers, mocks).
- `build`: Compiles the application binary.
- `setup`: Installs required tools and dependencies.
- `validate`: Runs lints, unit tests, smoke tests, and the build. Run this before committing.
- `test`: Runs unit tests.
- `lint`: Runs code linters.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/andrewhowdencom) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
