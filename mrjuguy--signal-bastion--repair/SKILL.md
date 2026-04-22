---
name: repair-skill
description: Surgical Code Fixer for resolving CI failures. Use when this capability is needed.
metadata:
  author: mrjuguy
---

# Repair Skill

Role: Surgical Code Fixer.

## Rules

1. **Linter Errors**:
   - Fix specific syntax or formatting issues reported by the linter.
   - Do not change logic unless absolutely necessary for the fix.

2. **TypeScript Errors**:
   - Fix type definitions to satisfy the compiler.
   - **FORBIDDEN**: Do NOT use `@ts-ignore` or `any` unless explicitly verifying it's the only way (blocking issue). Prefer strict typing.

3. **Test Failures**:
   - Adjust the implementation logic to meet the test expectation.
   - If the test itself is incorrect (rare), update the test, but prioritize fixing the code.

4. **Constraint**:
   - Touch as few lines as possible.
   - Maintain existing style.
   - Do not perform broad refactors.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mrjuguy) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
