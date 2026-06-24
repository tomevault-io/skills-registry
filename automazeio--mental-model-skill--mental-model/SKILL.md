---
name: mental-model
description: Maintains a living mental model of the codebase. Read mental-model.md before making changes, update it when discovering new patterns or behaviors. Use when this capability is needed.
metadata:
  author: automazeio
---

# Mental Model

When working on this codebase, first check for `mental-model.md` in the project root.

## If it exists

Read it to understand the codebase architecture, patterns, conventions, and relationships before making changes, when exploring unfamiliar parts of the code, or debugging failed tests or unexpected behavior.

Update it whenever you discover something new — from running tests, hitting unexpected behavior, or exploring unfamiliar parts of the code.

## If it doesn't exist

Ask the user if they'd like you to create one. If confirmed:

Create a new file named `mental-model.md` at the repo root, that builds and maintains a full mental model of the codebase.

Scan the entire codebase and document how the system works end-to-end (architecture, key modules, data flow, relationships, testing approach, Docker/setup, etc).

Treat this as a living document. Update it whenever you learn something new from running tests or hitting unexpected behavior.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/automazeio) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
