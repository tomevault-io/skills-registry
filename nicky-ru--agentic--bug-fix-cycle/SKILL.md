---
name: bug-fix-cycle
description: Bug fixing workflow. Guides systematic debugging from symptom to fix. Use this skill when fixing bugs, debugging issues, or addressing error reports. Use when this capability is needed.
metadata:
  author: nicky-ru
---

# Bug Fix Workflow

This skill defines the bug fixing workflow. It guides systematic debugging from initial symptom to verified fix.

## Cycle Overview

```text
INVESTIGATE → REPRODUCE → TEST → FIX → COMMIT
```

| Phase       | Agent                | Entry                 | Exit Criteria                    |
|-------------|----------------------|-----------------------|----------------------------------|
| INVESTIGATE | review-agent         | Bug report/symptom    | Root cause identified            |
| REPRODUCE   | review-agent         | Root cause hypothesis | Bug reliably reproducible        |
| TEST        | tdd-test-writer      | Reproduction steps    | Failing regression test          |
| FIX         | implementation-agent | Failing test          | Test passes                      |
| COMMIT      | (human decision)     | Fix complete          | Changes committed                |

Human approval required before each phase transition.

## Phase Details

### INVESTIGATE: Find Root Cause

**Agent**: review-agent

**Actions**:

1. Read bug report or symptom description
2. Create `.bugfix_context.md` with initial symptom
3. Trace code paths related to the symptom
4. Read error logs, stack traces if available
5. Form root cause hypothesis
6. Document findings in `.bugfix_context.md`

**Output**: Root cause hypothesis with supporting evidence

**Exit**: Human approves hypothesis → proceed to REPRODUCE

### REPRODUCE: Confirm the Bug

**Agent**: review-agent

**Actions**:

1. Design minimal reproduction steps
2. Execute reproduction to confirm bug exists
3. Document exact steps in `.bugfix_context.md`
4. Note any environment or data dependencies

**Output**: Reproducible test case (manual steps or script)

**Exit**: Bug reliably reproduced → proceed to TEST

### TEST: Write Regression Test

**Agent**: tdd-test-writer

**Actions**:

1. Read `.bugfix_context.md` for reproduction steps and root cause
2. Write failing test that captures the bug
3. Run test to confirm failure
4. Update `.bugfix_context.md` with test location

**Rules**:

- Test should fail for the exact reason identified in INVESTIGATE
- Test name should describe the bug: `should_handle_special_chars_in_password`
- Keep test minimal — only what's needed to catch this bug

**Exit**: Failing test approved → proceed to FIX

### FIX: Implement Minimal Fix

**Agent**: implementation-agent

**Actions**:

1. Read `.bugfix_context.md` for context
2. Implement minimal fix to pass the regression test
3. Run full test suite to ensure no regressions
4. Update `.bugfix_context.md` with fix description

**Rules**:

- Fix only what's broken — no scope creep
- Don't refactor unrelated code
- If fix reveals deeper issues, document them for separate work

**Exit**: All tests pass → proceed to COMMIT

### COMMIT: Commit the Fix

**Actions**:

1. Stage regression test and fix
2. Generate commit message with `fix:` prefix
3. Human approves commit
4. Commit changes

**Commit Message Format**:

```
fix: <short description>

<optional body explaining the root cause and fix>

Closes #<issue-number> (if applicable)
```

**Exit**: Committed → bug fix cycle complete

## Context File

`.bugfix_context.md` tracks state across phases:

```markdown
# Bug Fix Context

**Bug**: Login fails for users with special characters in password
**Reported**: 2026-01-29
**Current Phase**: FIX

## Symptom

Users with passwords containing `&` or `=` cannot log in.
Error: "Invalid credentials" even with correct password.

## Investigation Findings

- Auth endpoint at `src/auth/login.ts:45`
- Password passed through URL query param (line 52)
- Special chars not URL-encoded before comparison
- Root cause: `&` splits the query string prematurely

## Reproduction Steps

1. Create user with password `test&pass=123`
2. Attempt login via POST /auth/login
3. Observe 401 response

## Regression Test

Location: `test/auth/login.test.ts`
Test: `should_authenticate_user_with_special_chars_in_password`

## Fix Description

URL-encode password before query string construction in `src/auth/login.ts:52`
```

## When to Use This Workflow

Use bug-fix-cycle when:

- Fixing reported bugs or errors
- Debugging unexpected behavior
- Addressing failing tests in production
- Investigating user-reported issues

Do NOT use for:

- New features → use feature-cycle
- Refactoring working code → use refactor-agent directly
- Pure investigation with no fix needed → exit early after INVESTIGATE

## Anti-Patterns

- Skipping INVESTIGATE and guessing the fix
- Fixing without a regression test
- Expanding scope beyond the reported bug
- Committing without running full test suite

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/nicky-ru) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
