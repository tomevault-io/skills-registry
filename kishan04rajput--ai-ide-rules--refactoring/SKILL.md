---
name: refactoring
description: Improving internal code structure without changing external behavior. Use when this capability is needed.
metadata:
  author: kishan04rajput
---

# Refactoring Skill

Use this skill strictly for **behavior-preserving** code improvements. If you need to fix a bug or add functionality, use the `bug-fixing` or standard coding flow instead.

## Core Principles
1.  **Zero Behavior Change**: The code MUST produce the same output for the same input after refactoring.
2.  **Small, Reversible Steps**: Make incremental changes that can be easily reverted if a test fails.
3.  **Clean Code Focus**: Focus on readability, maintainability (DRY, SOLID), and removing technical debt.

## Refactoring Process
1.  **Baseline**: Ensure existing tests pass. If no tests exist, write them before refactoring.
2.  **Identify Smells**: Long methods, dead code, logic duplication, or poor naming.
3.  **Apply Pattern**: Use established patterns like "Extract Method", "Rename Variable", or "Simplified Conditionals".
4.  **Verify**: Run tests after every small change to confirm no breakage.

> [!TIP]
> Refactoring is a "clean as you go" process. It should make future changes easier, not change what the code does today.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/kishan04rajput) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
