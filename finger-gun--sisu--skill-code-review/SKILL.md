---
name: code-review
description: Perform a structured code review for safety, correctness, and maintainability Use when this capability is needed.
metadata:
  author: finger-gun
---

# Code Review

Use this skill to perform a systematic code review focused on correctness, safety, and maintainability.

## Goals

- Validate behavior against requirements and edge cases
- Identify security risks and unsafe patterns
- Ensure error handling and logging are correct
- Check performance hotspots and unnecessary allocations
- Confirm code aligns with SISU guidelines (explicit, composable, testable)

## Review Steps

1. **Scope the change**
   - Identify files changed and related components
   - Read relevant docs/specs/tests if available

2. **Behavior & correctness**
   - Walk through main flows and edge cases
   - Verify invariants and error handling

3. **Safety & security**
   - Look for unsafe file access, command injection, secrets handling
   - Validate input validation and schema enforcement

4. **Performance**
   - Check for unbounded loops or O(n^2) pitfalls
   - Validate caching and I/O patterns

5. **Tests**
   - Ensure tests cover happy path, edge cases, invalid input, and cancellation

## Output Format

- **Summary**: 2–4 bullets
- **Issues**: Prioritized list with severity and fix guidance
- **Suggestions**: Optional refactors or follow-ups

## Resources

See `resources/review-checklist.md` for a detailed checklist.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/finger-gun) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
