---
name: dead-code-eliminator
description: Find and delete unused files, disabled code blocks, Enhanced variants, deprecated features, test files in production for Leavn app Use when this capability is needed.
metadata:
  author: willsigmon
---

# Dead Code Eliminator

Find and delete dead code:

1. **Find candidates**:
   - Files with `#if false`
   - *Enhanced.swift variants
   - NavigationCoordinator (unused)
   - *Stub.swift files

2. **Verify unused**:
   ```bash
   grep -r "ClassName" LeavnApp/Sources/
   ```

3. **Delete safely**:
   - Remove from Xcode project
   - Delete file
   - Run build to verify
   - Commit deletion

Use when: Code cleanup, unused files, deprecated features, reducing codebase size

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/willsigmon) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
