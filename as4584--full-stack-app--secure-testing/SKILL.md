---
name: secure-testing
description: Test code snippets and logic in an isolated sandbox before applying them to the codebase. Use when this capability is needed.
metadata:
  author: as4584
---

# Secure Testing Skill

This skill ensures that complex logic or potentially breaking changes are verified in a sandboxed environment first.

## Instructions

1.  **Initialize Sandbox**: When implementing a complex algorithm or testing a third-party library snippet, use `sandbox_initialize` to create a clean environment.
2.  **Run Isolated Code**: Use `run_js_ephemeral` for quick logic checks or `run_js` for persistent testing sessions within the sandbox.
3.  **Verify Output**: Before writing code to the main `/home/lex/lexmakesit/` directory, ensure the logic works as expected in the sandbox.
4.  **Clean Up**: Always use `sandbox_stop` when the testing session is complete to save system resources.

## Tools to Use
- `sandbox_initialize`
- `sandbox_exec`
- `run_js`
- `run_js_ephemeral`
- `sandbox_stop`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/as4584) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
