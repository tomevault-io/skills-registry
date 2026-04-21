---
name: legacy-refactor
description: This skill guides the safe refactoring of "legacy" code (code without tests or difficult to understand code). Use when this capability is needed.
metadata:
  author: xingxerx
---
---
name: legacy-refactor
description: Specifically designed to guide agents through the process of modernizing old codebases without breaking existing dependencies.
---

# Legacy Refactor Protocol

This skill guides the safe refactoring of "legacy" code (code without tests or difficult to understand code).

## The Algorithm

1.  **Identify Seams**: A place where you can alter behavior without editing the place in question (e.g., via subclassing, interface extraction).
2.  **Characterization Tests**: Before changing ANY code, write tests that capture the *current* behavior (bugs and all).
    *   Goal: Ensure you don't change behavior unintentionally.
3.  **Break Dependencies**: Use dependency injection to isolate the code module.
4.  **Refactor**: Make your changes (clean code, upgrade lib, etc.).
5.  **Verify**: Run characterization tests.

## Strategies
*   **Sprout Method**: Write new logic in a new method and call it from the old one.
*   **Wrap Method**: Rename the old method, create a new method with the old name that calls the new logic + the old method.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/xingxerx) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
