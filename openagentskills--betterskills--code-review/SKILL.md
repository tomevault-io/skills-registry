---
name: code-review
description: >- Use when this capability is needed.
metadata:
  author: openagentskills
---

# Code Review

## Quick start

1. Understand the change goal (read PR description or ask).
2. Read the diff in logical chunks (not only file order).
3. Apply the checklist below.
4. Report findings by severity with file/line references when possible.

## Review checklist

- [ ] **Correctness** — Logic handles happy path and edge cases
- [ ] **Security** — No injection, auth bypass, secret leakage, unsafe deserialization
- [ ] **API/contracts** — Public surfaces backward-compatible unless versioned
- [ ] **Performance** — No obvious N+1, unbounded memory, or hot-path regressions
- [ ] **Maintainability** — Clear names, focused functions, matches project style
- [ ] **Tests** — Behavior covered; tests assert outcomes not implementation details
- [ ] **Docs** — User-facing changes documented

## Output format

```markdown
## Summary
[1–2 sentences: overall recommendation]

## Findings

### Critical (must fix)
- `path:line` — Problem. Suggested fix.

### Suggestions
- `path:line` — Improvement.

### Nitpicks (optional)
- Minor style or clarity notes.

## Verdict
[Approve / Request changes / Comment only]
```

## Severity guide

| Level | When |
|-------|------|
| Critical | Bugs, security issues, data loss risk |
| Suggestion | Better design, clearer code, missing tests |
| Nitpick | Style not enforced by linter |

## Additional resources

- Extended standards: [reference.md](reference.md)

---
> Source: [openagentskills/betterskills](https://github.com/openagentskills/betterskills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
