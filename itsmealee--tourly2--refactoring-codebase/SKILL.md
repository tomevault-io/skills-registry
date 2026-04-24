---
name: refactoring-codebase
description: Guidelines for maintaining a clean codebase through limits and abstraction. Use when a file or component becomes difficult to read. Use when this capability is needed.
metadata:
  author: itsmealee
---

# Refactoring and Code Cleanup

## When to use this skill
- A component file exceeds **200 lines**.
- The same logic is repeated in 3 or more places.
- Props are becoming too many/complex.

## Rules
- **The "Boy Scout Rule"**: Always leave the code slightly cleaner than you found it.
- **Sub-components**: Split large components into smaller files in a local `components/` subfolder.
- **Custom Hooks**: Extract stateful logic (complex forms, complex filters) into hooks.

## Workflow
- [ ] Identify a "Messy" component.
- [ ] Extract presentational parts into atomic components.
- [ ] Extract logic into Services or Hooks.
- [ ] Verify that the visual UI has NOT changed.

## Instructions
- **No Pre-optimization**: Refactor logic only when it's actually reused or the file is unmanageable.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/itsmealee) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
