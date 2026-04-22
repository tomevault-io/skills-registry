---
name: check-production-ready
description: Verify code is ready for production - build, tests, security, and configuration checks Use when this capability is needed.
metadata:
  author: whoabuddy
---

# Check Production Ready Skill

Validate that code meets production deployment standards.

## Usage

```bash
/check-production-ready        # Full production readiness check
```

## What Gets Checked

| Category | Checks |
|----------|--------|
| Build | Compiles without errors |
| Tests | All pass, none skipped without reason |
| Linting | ESLint, TypeScript, Clarinet |
| Security | npm audit, no hardcoded secrets |
| Environment | .env.example exists, no dev URLs |
| Git | Clean working directory, correct branch |

## Output

Checklist with pass/fail status and summary of any blockers or warnings.

## Runbook

Full procedure: `runbook/check-production-ready.md` in your knowledge base.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/whoabuddy) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
