---
name: security-audit
description: > Use when this capability is needed.
metadata:
  author: ruvnet
---

# Security Audit Skill

## Purpose
Security scanning and vulnerability detection.

## When to Trigger
- authentication
- authorization
- payment processing
- user data

## When to Skip
- read-only operations
- internal tooling

## Commands

### Full Security Scan
Run comprehensive security analysis

```bash
npx @claude-flow/cli security scan --depth full
```

### Input Validation Check
Check for input validation issues

```bash
npx @claude-flow/cli security scan --check input-validation
```



## Best Practices
1. Check memory for existing patterns before starting
2. Use hierarchical topology for coordination
3. Store successful patterns after completion
4. Document any new learnings

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ruvnet) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
