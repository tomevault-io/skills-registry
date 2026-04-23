---
name: ship-checklist
description: > Use when this capability is needed.
metadata:
  author: kevcofett
---

# Ship Checklist

## Commands

- `/ship` — Run the full pre-deployment checklist
- `/ship --quick` — Quick check (critical items only)
- `/ship --report` — Generate a deployment readiness report

## Checklist Categories

### 1. Testing

- [ ] All unit tests passing
- [ ] Integration tests passing
- [ ] No skipped or disabled tests without documented reason
- [ ] Test coverage meets project threshold

### 2. Code Quality

- [ ] No CRITICAL issues from /review
- [ ] No TODO/FIXME comments in changed files
- [ ] No console.log/print debug statements
- [ ] Linting passes with zero errors

### 3. Configuration

- [ ] Environment variables documented in .env.example
- [ ] No hardcoded URLs, paths, or credentials
- [ ] Config values externalized and environment-specific
- [ ] Feature flags set correctly for target environment

### 4. Security

- [ ] No secrets in source code or commit history
- [ ] Dependencies audited (no critical CVEs)
- [ ] API keys and tokens rotated if compromised
- [ ] Authentication/authorization working correctly

### 5. Solution Integrity (MCMAP-specific)

- [ ] CMAP validation passes with zero errors
- [ ] Solution version matches across filename, solution.xml
- [ ] All displaynames use displayname tags (not description)
- [ ] OptionSet prefixes follow prefix_fieldname convention
- [ ] Connection references defined in customizations.xml
- [ ] Sandbox import succeeds

### 6. Deployment

- [ ] Deployment branch is up to date with main
- [ ] Rollback plan documented
- [ ] Monitoring and alerting configured
- [ ] Stakeholders notified of deployment window

## Procedure

1. Run through each category sequentially
2. Mark each item PASS or FAIL
3. Any FAIL in categories 1-4 = deployment blocked
4. Any FAIL in category 5 = MCMAP deployment blocked
5. Category 6 FAILs are warnings (can proceed with acknowledgment)
6. Generate summary report

## Output

```
## Ship Checklist: {date}

### Verdict: READY / BLOCKED

| Category | Status | Details |
|----------|--------|---------|
| Testing | PASS | 47/47 tests passing |
| Code Quality | PASS | Zero issues |
| Configuration | PASS | All vars documented |
| Security | PASS | No vulnerabilities |
| Solution | PASS | v7.12.5 validated |
| Deployment | WARN | Rollback plan pending |

Blocking issues: {none or list}
```

## MCMAP Deployment Pipeline

The full MCMAP ship sequence:
1. /review — Code review
2. /deps scan --security — Security audit
3. make validate SOL=solution.zip — CMAP validation
4. pac solution import (sandbox) — Sandbox test
5. /ship — Final checklist
6. pac solution import (production) — Production deploy

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/kevcofett) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
