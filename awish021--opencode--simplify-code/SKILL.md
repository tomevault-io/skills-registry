---
name: simplify-code
description: Proactively simplify recently modified code while preserving exact behavior. Use when this capability is needed.
metadata:
  author: awish021
---

# Simplify Code

## What I do

I simplify recently modified code to improve clarity, consistency, and maintainability without changing behavior.

## When to use this

Use this skill proactively after code is written or modified, unless the user asks for a different scope.

## Principles

1. Preserve behavior exactly. Do not change outputs, side effects, or external contracts.
2. Favor clarity over brevity. Explicit, readable code is preferred to compact or clever code.
3. Reduce complexity. Simplify control flow, reduce nesting, and remove redundant logic.
4. Keep scope focused. Only touch recently modified code unless the user explicitly asks for broader refactoring.
5. Respect project conventions. Follow existing naming, formatting, and structural patterns.

## Guardrails

- Do not change public APIs or documented behavior.
- Do not alter performance characteristics unless explicitly requested.
- Avoid micro-optimizations that reduce readability.
- Avoid clever tricks, dense one-liners, or nested ternaries.
- Do not remove useful abstractions that improve organization.
- Do not add comments unless they clarify a non-obvious block.

## Process

1. Identify recently modified sections.
2. Look for unnecessary complexity or duplication.
3. Simplify with clear names and straightforward structure.
4. Verify behavior is unchanged.
5. Document only meaningful changes that affect understanding.

## Output expectations

- Cleaner, more readable code with the same functionality.
- Minimal, targeted edits focused on the modified areas.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/awish021) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
