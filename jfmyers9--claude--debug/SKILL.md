---
name: debug
description: > Use when this capability is needed.
metadata:
  author: jfmyers9
---

# Debug

Systematically diagnose and fix bugs, CI failures, and
test failures.

## Arguments

- `<task-id>` — debug that specific issue
- `<error-description>` — debug that problem
- (no args) — check for failing tests/CI on current branch

## Workflow

### 1. Issue Setup

**If task ID provided:**
- TaskGet(taskId) to load context
- TaskUpdate(taskId, status: "in_progress")

**If no task ID:**
- TaskCreate:
  - subject: "Debug: <problem>"
  - description: "Steps to Reproduce: <observed symptoms and error output>. Acceptance Criteria: Bug is fixed and verified by passing tests."
  - metadata: {type: "bug", priority: 1}

### 2. Diagnose and Fix

Gather context, trace to root cause, make minimal fix, verify
with tests. Update task on success or record findings on failure.

### 5. Report

Output format:
```
## Problem
- [what was wrong]

## Root Cause
- [traced cause]

## Fix Applied
- [minimal changes made]

## Verification
- [test results / CI status]
```

## Style Rules

- Keep concise — bullet points, not prose
- No emoji
- Steps numbered and actionable
- Prefer reading error output over guessing
- Make minimal fixes — don't refactor surrounding code
- Use parallel tool calls when gathering independent data

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jfmyers9) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
