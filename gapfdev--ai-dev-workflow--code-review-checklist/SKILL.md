---
name: code-review-checklist
description: Systematic code review checklist for quality, security, and maintainability Use when this capability is needed.
metadata:
  author: gapfdev
---

# Code Review Checklist

Skill for systematically reviewing code to verify quality, security, and maintainability.

## Input
- Source code to review (PR, feature branch, or specific files)
- Tests associated with the code

## Output
- Review report with findings classified by severity
- Decision: Approve / Approve with conditions / Reject

## Process

### Phase 1: Run Tests
```bash
# Run the project's complete test suite
# The specific command depends on the stack
```
- If **any test fails** → REJECT immediately
- If **all pass** → Continue review

### Phase 2: Review Checklist

#### Correctness
- [ ] Does the code do what the ticket/requirement says?
- [ ] Are acceptance criteria covered?
- [ ] Does it handle errors correctly (try/catch, null checks)?

#### Readability
- [ ] Are variable/function names descriptive?
- [ ] Is the code structure clear without needing comments?
- [ ] Are functions short (< 30 lines)?
- [ ] Do classes have a single responsibility (SRP)?

#### Maintainability
- [ ] No duplicated code (DRY)?
- [ ] Dependencies are injected (not hardcoded)?
- [ ] Follows the project's established patterns?

#### Performance
- [ ] No heavy operations on the main thread?
- [ ] Lists/collections handled efficiently?
- [ ] No obvious memory leaks?

#### Security
- [ ] No hardcoded secrets/keys?
- [ ] No sensitive data logged, traced, or exposed at runtime (including debug paths)?
- [ ] Sensitive variables never appear in error messages, stack traces, or observability outputs?
- [ ] User inputs validated/sanitized?
- [ ] APIs use HTTPS?
- [ ] Sensitive data stored securely?

#### Tests
- [ ] Adequate coverage of new code?
- [ ] Cover happy path + edge cases? (Enumerate the specific edge cases covered. Are boundary values included?)
- [ ] Tests are independent of each other?
- [ ] Test names are descriptive?

### Phase 3: Classify Findings

| Severity | Meaning | Action |
|----------|---------|--------|
| 🔴 **Critical** | Bug, security issue, test failure | BLOCKS approval |
| 🟡 **Warning** | Code smell, important improvement | Should be fixed |
| 🔵 **Info** | Suggestion, nice-to-have | Optional |

### Phase 4: Decision

```
Any 🔴 Critical findings?
├── Yes → REJECT → Return with list of corrections
└── No → Any 🟡 Warning findings?
     ├── Yes → APPROVE WITH CONDITIONS → Fix before continuing
     └── No → APPROVE
```

## Rules
1. **NEVER** approve code with failing tests
2. **NEVER** approve code with Critical findings
3. **ALWAYS** run tests before reviewing code
4. **ALWAYS** classify each finding with severity

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/gapfdev) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
