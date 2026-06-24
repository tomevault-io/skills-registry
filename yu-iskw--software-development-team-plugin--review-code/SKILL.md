---
name: review-code
description: Review code changes for quality, security, and adherence to project conventions. Use after making code changes or when reviewing a pull request. Use when this capability is needed.
metadata:
  author: yu-iskw
---

# Review Code

Review the following code:

$ARGUMENTS

## Review Scope

1. Run `git diff` to identify changes (or examine specified files)
2. Review each changed file against the checklist below
3. Report findings organized by severity

## Review Checklist

### Correctness

- Logic handles all cases including edge cases
- Async operations properly awaited
- Error handling is appropriate

### Security

- No hardcoded secrets
- Input validation at boundaries
- No injection vectors (command, XSS, prompt)

### Code Quality

- Proper type annotations where applicable
- No unnecessary `any` types
- Interfaces defined for data structures

### Performance

- No unnecessary re-renders (UI components)
- Efficient algorithms and data structures

### Architecture

- Follows project patterns
- Correct component boundaries
- No circular dependencies

## Output

Provide findings as:

- 🔴 **Critical**: Must fix before merge
- 🟡 **Warning**: Should fix
- 🔵 **Suggestion**: Consider improving

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/yu-iskw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
