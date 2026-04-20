---
name: code-practices
description: Standards for self-documenting code and comment usage Use when this capability is needed.
metadata:
  author: ishankgulati
---

# Code Best Practices

## Comments
- **No Trivial Comments**: Strictly avoid commenting on *what* the code does if the syntax makes it obvious.
  - **Forbidden**: `count++ // increment count`
  - **Forbidden**: `if err != nil { // check error`
- **Intent Over Implementation**: Use comments only to explain *why* a specific decision was made or to clarify complex business logic.

## Naming Conventions
- **Self-Documenting Code**: Choose method and variable names that explain their purpose without needing a comment.
- **Specific over Generic**:
  - `fetchUserData(userID string)` instead of `getData(id string)`
  - `invoiceProcessingQueue` instead of `queue` or `q`
- **Boolean Variables**: Should sound like questions (e.g., `isValid`, `hasAccess`, `fileHere`).

## Philosophy
- Code is read much more often than it is written.
- If you feel the need to write a comment to explain *what* a block does, consider refactoring the code into a named function instead.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ishankgulati) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
