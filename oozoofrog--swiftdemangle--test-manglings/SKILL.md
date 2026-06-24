---
name: test-manglings
description: Validate mangled symbols against expected demangled output Use when this capability is needed.
metadata:
  author: oozoofrog
---

# test-manglings

Given a mangled Swift symbol:

1. Check if it exists in `Tests/SwiftDemangleTests/Resources/manglings.txt`
2. Run `swift test --filter testManglings` to verify demangling results
3. Report pass/fail with the expected vs actual demangled output

If a new symbol pair is provided (mangled ---> demangled), add it to the appropriate resource file and run the test to confirm.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/oozoofrog) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
