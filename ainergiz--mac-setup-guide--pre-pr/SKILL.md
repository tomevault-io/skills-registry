---
name: pre-pr
description: Review code changes against spec for drift and principle violations before PR submission. Use when user says "pre-pr review", "check against spec", "review my changes", "spec drift", "am I following the spec", or before creating a pull request. Use when this capability is needed.
metadata:
  author: ainergiz
---

# Pre-PR Spec Compliance Review

Review code changes against the original spec to detect drift and principle violations.

## What This Does

1. Detect **spec drift** - did implementation deviate from plan?
2. Check **test coverage** - does new code have tests?
3. Detect **breaking changes** - API/DB changes that need coordination
4. Check **principle compliance** - YAGNI, simplicity, etc.
5. Offer remediation for issues found

## Workflow

```
Phase 1: Get Issue Context   → Ask for issue number
Phase 2: Fetch Spec          → Use gh-issues skill patterns
Phase 3: Get Code Changes    → git diff main...HEAD
Phase 4: Quality Checks      → Tests + breaking changes
Phase 5: Code Analysis       → Spec drift + principles
Phase 6: Summary Report      → Action items + follow-ups
```

## Phase 1: Get Issue Context

**If no issue provided:** Ask user for the GitHub issue number this work relates to.

## Phase 2: Fetch Spec Context

```bash
# Current issue
gh issue view <number> --json number,title,body,labels,state,url

# Check for parent (full spec)
gh api graphql -f query='{ repository(owner: "{owner}", name: "{repo}") {
  issue(number: <number>) { parent { number title } }
} }'

# If parent exists, fetch it
gh issue view <parent-number> --json number,title,body

# List sibling sub-issues
gh sub-issue list <parent-number>
```

Present spec summary:

```
## Spec Context Loaded

Parent: #<parent> - <title>
Current: #<number> - <title>
Siblings: #<sib1>, #<sib2>

### Current Sub-issue Scope
<body>

### Parent Spec Summary
<Goals, Non-Goals, Technical Design, Acceptance Criteria>
```

## Phase 3: Get Code Changes

```bash
# Changes against main
git diff main...HEAD --stat
git diff main...HEAD

# Uncommitted changes
git diff --stat
git diff
```

Present:

```
## Code Changes

Commits since main: <count>
Files changed: <count>

Changed files:
- <file1> (+X, -Y)
- <file2> (+X, -Y)
```

## Phase 4: Quality Checks

### Test Coverage

Identify new/modified code and check for corresponding tests:

```bash
find . -name "*test*" -o -name "*spec*" | grep -i <changed-file-pattern>
```

| Finding                   | Action                    |
| ------------------------- | ------------------------- |
| New function without test | ⚠️ Suggest adding test    |
| Critical path untested    | 🚨 Require test before PR |

### Breaking Changes

Check for API/DB changes:

```bash
git diff main...HEAD --name-only | grep -i "migration\|schema"
```

| Change Type               | Required Action  |
| ------------------------- | ---------------- |
| Removed API endpoint      | Deprecation plan |
| Changed required params   | Migration guide  |
| DB column removed/renamed | Migration script |

Use AskUserQuestion for any issues found:

- Options: ["Fix now", "Explain why acceptable", "Create follow-up issue"]

## Phase 5: Code Analysis

### Spec Drift

→ Read [references/drift-analysis.md](references/drift-analysis.md)

For EACH drift found, ask user if intentional.

### Principle Compliance

→ Read [references/principles-review.md](references/principles-review.md)

Check: YAGNI, Simplicity, Boring Tech.

## Phase 6: Summary Report

```
## Pre-PR Review Complete

### Test Coverage
- New code paths: <count> | With tests: <count> | Missing: <count>

### Breaking Changes
- API changes: [✓ None / ⚠ <count> detected]
- DB migrations: [✓ None / ⚠ <count> detected]

### Spec Drift Summary
- Total: <count> | Intentional: <count> | To address: <count>

### Principle Compliance
- YAGNI: [✓ / ⚠ issues]
- Simplicity: [✓ / ⚠ issues]
- Complexity: [✓ / ⚠ issues]
- Tech Choices: [✓ / ⚠ issues]

### Action Items Before PR
- [ ] <action>

### Follow-up Issues to Create
- [ ] <issue>
```

Execute approved actions (spec updates, new issues) using `gh-issues` skill.

After review passes, use the `create-pr` skill to create the pull request.

## Notes

- Be strict about flagging drift - let user decide if acceptable
- Use AskUserQuestion for every drift/violation
- Don't auto-fix - present options, let user decide
- This is review only - use `create-pr` skill for actual PR creation

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ainergiz) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
