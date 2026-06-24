---
name: code-review
description: Reviews code changes for quality, security, and best practices. Use when this capability is needed.
metadata:
  author: agentjido
---

# Code Review

Review code changes, diffs, and modified files for quality and correctness.

## When to Use

Activate when users ask to:
- Review code changes or diffs
- Check code quality
- Analyze uncommitted work
- Review changes since diverging from a branch

## Workflow

1. **Identify Changes**
   - Use `git_diff` to get the current diff
   - Identify files that have been modified

2. **Analyze Each File**
   - Read the full file context with `read_file`
   - Understand the purpose of changes
   - Check for potential issues

3. **Provide Feedback**
   - Note any bugs or logic errors
   - Suggest improvements
   - Highlight security concerns
   - Comment on code style and conventions

## Review Criteria

- **Correctness**: Does the code do what it's supposed to?
- **Security**: Are there any vulnerabilities?
- **Performance**: Are there obvious inefficiencies?
- **Maintainability**: Is the code readable and well-structured?
- **Testing**: Are changes adequately tested?

## Response Format

Provide structured feedback:
1. Summary of changes
2. Specific issues found (if any)
3. Suggestions for improvement
4. Overall assessment

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/agentjido) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
