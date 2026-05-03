---
name: extract-function
description: Extract selected TypeScript/JavaScript code into a new helper function and replace the selection with a function call. Use when the user asks to extract code, reduce function complexity, or deduplicate repeated logic while keeping behavior unchanged. Use when this capability is needed.
metadata:
  author: dbradf
---

# Extract Function

## When to use

Use this skill when working in TypeScript or JavaScript and the user asks to:

- extract selected logic into a function
- reduce complexity inside a large function
- deduplicate repeated blocks by introducing a shared helper
- refactor a function without changing behavior

## Workflow

1. Analyze the selected block.
   - Identify required inputs (variables read from outer scope).
   - Identify outputs (return value, mutated values, or control flow effects).
   - Check side effects (state updates, logging, I/O, exceptions).
2. Design the extracted function.
   - Choose a specific verb-based name that describes intent.
   - Pass dependencies as parameters instead of capturing implicit outer state.
   - For TypeScript, keep parameter and return types explicit when helpful and consistent with local style.
3. Place the new function correctly.
   - Insert the extracted function immediately after the current function by default.
   - If the file already has a local helper pattern, match naming and declaration style.
4. Replace the selected code with a function call.
   - Preserve execution order and existing behavior.
   - Preserve error handling and return semantics.
   - Ensure async behavior stays correct (`await`, returned promises, or sync flow).
5. Validate the refactor.
   - Confirm no hidden dependencies remain in the extracted body.
   - Confirm all required values are passed explicitly.
   - Run lint/tests when available, or state what could not be run.

## Placement rule

Unless the user explicitly requests otherwise, always insert the extracted function directly after the current function where extraction occurred.

## Quality checklist

Before finalizing:

- extracted function name is specific and intent-revealing
- function parameters include all external dependencies used by the selection
- no behavior change was introduced
- no unintended scope coupling remains
- call site is simpler and easier to read
- extracted function is neither overly generic nor overly broad

## Suggested response format

When reporting work, use:

```markdown
Extracted selected code into: <functionName>

What changed:
- Replaced selected block with `<functionName>(...)` call
- Added `<functionName>` immediately after `<currentFunctionName>`
- Preserved existing behavior and control flow

Verification:
- Ran: <command or "not run">
- Result: <pass/fail/not run>

Notes:
- Inputs passed: <key params>
- Output/side effects: <brief summary>
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/dbradf) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
