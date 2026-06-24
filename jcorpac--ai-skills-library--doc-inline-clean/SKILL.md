---
name: doc-inline-clean
description: Guidelines for writing self-documenting code and knowing when (and how) to use comments. Use when this capability is needed.
metadata:
  author: jcorpac
---

# Inline Documentation & Clean Code

Code should be written for human readers first and computers second.

## Self-Documenting Code
The best documentation is code that reveals its intent without comments:
- **Descriptive Names**: `calculate_interest_rate()` instead of `cir()`.
- **Small Functions**: Break complex logic into smaller, well-named pieces.
- **Explain with Code**: `is_valid_user = user.active and not user.banned` instead of a comment explaining the `if` condition.

## When to Comment
- **Explain WHY, not WHAT**: Don't comment what the code does; explain the non-obvious rationale behind it.
- **Legal/Attribution**: For licenses or inherited complex algorithms.
- **Warnings**: "Don't change this order, it will break [Service X]".
- **TODOs**: Tracking debt or future work (but keep these to a minimum).

## Bad Comments
- **Redundant**: `i = i + 1  # Increment i`.
- **Stale**: Comments that refer to old versions of code.
- **Zombie Code**: Commented-out blocks of code that "we might need later". (Use Git for that instead).

## Summary
If you feel the need to write a comment explaining *how* something works, consider refactoring the code to be clearer instead.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jcorpac) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
