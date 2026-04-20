---
name: review
description: Code review checklist Use when this capability is needed.
metadata:
  author: mihow
---

# Code Review

Review the specified code for quality and correctness.

## Review Checklist

### Correctness
- [ ] Logic handles all cases correctly
- [ ] Edge cases are handled
- [ ] Error handling is appropriate

### Security
- [ ] No hardcoded secrets or credentials
- [ ] Input validation at boundaries
- [ ] No injection vulnerabilities

### Style
- [ ] Follows project conventions
- [ ] Names are clear and descriptive
- [ ] No dead code or commented-out blocks

### Testing
- [ ] Tests cover the changes
- [ ] Tests are meaningful (not just coverage)
- [ ] Edge cases are tested

### Performance
- [ ] No obvious inefficiencies
- [ ] Resource cleanup (files, connections)

## Usage

```
/review src/my_project/core.py
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mihow) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
