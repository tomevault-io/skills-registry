---
name: code-review-expert
description: Comprehensive code review expertise. Use when reviewing code, evaluating architecture, or assessing quality. Triggers on review, evaluate, assess, audit, code quality, best practices. Use when this capability is needed.
metadata:
  author: aiskillstore
---

# Code Review Expert

Comprehensive code review expertise for evaluating architecture and quality.

## When to Use

- Reviewing code changes before merge
- Evaluating architecture decisions
- Assessing code quality
- Auditing for security issues
- Performing pre-commit reviews

## Workflow

### Step 1: Initial Scan (10%)

Review structure and architecture overview.

### Step 2: Top-Down Review (40%)

Architecture → Modules → Functions.

### Step 3: Multi-Perspective (30%)

Review as Architect, PM, QA, UX.

### Step 4: Deep Dives (15%)

Focus on security and performance.

### Step 5: Report (5%)

Summarize and prioritize findings.

---

## Review Phases
```
Phase 1 (10%): Initial scan - structure, architecture
Phase 2 (40%): Top-down - Architecture → Modules → Functions
Phase 3 (30%): Multi-perspective - Architect, PM, QA, UX
Phase 4 (15%): Deep dives - Security, performance
Phase 5 (5%):  Report - Summarize, prioritize
```

## Severity

| Level | Action |
|-------|--------|
| 🔴 Critical | Must fix before deploy |
| 🟠 High | Fix this sprint |
| 🟡 Medium | Fix next sprint |
| 🟢 Low | Backlog |

## Quick Checklist

- [ ] No `any` types
- [ ] Error handling complete
- [ ] Input validation present
- [ ] No hardcoded secrets
- [ ] Parameterized queries
- [ ] Async errors handled

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/aiskillstore) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
