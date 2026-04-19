---
name: haskell
description: Guides for Haskell code. Use when writing Haskell code. Use when this capability is needed.
metadata:
  author: langston-barrett
---

- Avoid defining functions by cases on their last argument, use `LambdaCase`
- Avoid list indexing (`!!`)
- Avoid partial functions (`head`, `undefined`) except in tests
- Instead of nesting `Either`, `Maybe`, and tuples, make a new `data` type
- `LambdaCase` now supports `\cases` for multiple arguments
- Never return tuples of arity higher than 2, just make a new `data` type
- Never use `unsafeCoerce` or `unsafePerformIO`
- Only use `Either` for error handling, otherwise make a new `data` type
- Use `Data.Text.IO` for I/O

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/langston-barrett) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
