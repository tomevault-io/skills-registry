---
name: bughunt
description: Use when performing comprehensive bug hunting, code auditing, or finding all issues in a codebase before release or after major changes
metadata:
  author: wilrf
---

# Bughunt Skill

Comprehensive bug hunting that dispatches parallel agents to find bugs across all layers,
then deduplicates findings and produces a prioritized BUGHUNT.md report.

## The Iron Law

```
WHEN YOU THINK YOU'RE DONE, YOU'RE NOT.
```

Every hunter follows three passes. The second pass catches the bugs that hide after you feel "done."

## Severity & Confidence Rubric

All hunters use the same two-axis classification:

**Severity**

| Level | Definition |
|-------|-----------|
| CRITICAL | Data loss, auth bypass, RCE, or crash in production |
| HIGH | Functional breakage, security risk, or data corruption |
| MEDIUM | Degraded behavior, edge case failure, or bad UX |
| LOW | Code quality, minor inconsistency, non-critical smell |

**Confidence**

| Level | Definition |
|-------|-----------|
| HIGH | Direct evidence — code traced, pattern confirmed |
| MEDIUM | Strong pattern — likely real but not fully verified |
| LOW | Theoretical — possible concern without direct proof |

Only CRITICAL/HIGH findings at LOW confidence require a note explaining why they couldn't be verified.

## Quick Reference

| Hunter | Focus | Severity Range | Color |
|--------|-------|----------------|-------|
| frontend-hunter | React, CSS, a11y | CRITICAL–LOW | yellow |
| backend-hunter | API, logic, data | CRITICAL–LOW | cyan |
| type-safety-hunter | TypeScript, `any` | HIGH–LOW | blue |
| error-handling-hunter | try/catch, boundaries | HIGH–LOW | red |
| edge-case-hunter | null, empty, boundaries | HIGH–LOW | magenta |
| security-hunter | XSS, injection, secrets | CRITICAL–HIGH | red |
| database-hunter | queries, RLS, N+1 | CRITICAL–HIGH | green |
| auth-hunter | sessions, tokens, perms | CRITICAL–HIGH | red |
| api-hunter | endpoints, validation | HIGH–MEDIUM | cyan |
| env-hunter | config, env vars | HIGH–MEDIUM | yellow |
| performance-hunter | N+1, memory, bundle | HIGH–MEDIUM | magenta |
| test-hunter | flaky, coverage, assertions | MEDIUM–LOW | green |
| dependency-hunter | outdated, vulnerabilities | CRITICAL–MEDIUM | blue |

## Workflow

1. Detect tech stack (package.json, config files, directory structure)
2. Dispatch applicable hunters in PARALLEL based on detected stack
3. Collect findings from all hunters
4. **Deduplicate**: merge findings at the same `file:line` across hunters — use highest severity, credit all contributing hunters
5. Write BUGHUNT.md with prioritized findings
6. Offer to clear context and run `/bugfix`

## Stack-Gated Dispatch

Only dispatch hunters relevant to the detected stack:

- **All codebases**: security, backend, edge-case, error-handling, dependency, env, type-safety
- **Has frontend (React/Vue/HTML)**: + frontend, api
- **Has database (Supabase/Prisma/SQL)**: + database, performance
- **Has auth (JWT/sessions/OAuth)**: + auth
- **Has tests (Jest/Vitest/pytest)**: + test

## Output: BUGHUNT.md

```markdown
# Bug Hunt Report

**Date:** YYYY-MM-DD
**Stack:** [detected tech stack]
**Hunters dispatched:** N
**Deduplication:** N findings merged

## Summary
| Severity | Count | High Confidence | Low Confidence |
|----------|-------|-----------------|----------------|
| Critical | N | N | N |
| High | N | N | N |
| Medium | N | N | N |
| Low | N | N | N |

## Critical Issues

### [Bug Title]
- **File:** `path/to/file.ts:line`
- **Severity:** CRITICAL
- **Confidence:** HIGH | MEDIUM | LOW
- **Hunter(s):** security-hunter [, other-hunter if deduplicated]
- **Finding:** [What the bug is]
- **Evidence:** [Code snippet or trace]
- **Fix Suggestion:** [What to do]

## High Priority
[same format...]

## Medium Priority
[same format...]

## Low Priority
[same format...]
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/wilrf) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
