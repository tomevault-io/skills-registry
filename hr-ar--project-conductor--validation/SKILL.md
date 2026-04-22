---
name: running-validation-loops
description: Execute self-correcting validation workflow. Use when validation fails, tests fail, or build breaks. Use when this capability is needed.
metadata:
  author: hr-ar
---

# Validation Loop

## When to Use
- npm run validate fails
- CI/CD breaks
- Test or build failures

## Process
1. Capture full error output
2. Analyze root cause in context of examples/
3. Apply minimal fix aligned with patterns
4. Re-run validation
5. Repeat until success (max 5 attempts)

## Commands
```bash
npm run validate
```

## Success Criteria
- All gates pass
- Fix follows patterns in examples/
- No new anti-patterns introduced

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/hr-ar) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
