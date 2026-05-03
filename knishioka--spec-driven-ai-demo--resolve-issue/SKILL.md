---
name: resolve-issue
description: Implement from Issue specification. Creates plan.md (scope gate), implements code, runs tests, generates PR_DRAFT.md. Use after Issue is created or ISSUE_WITH_SPEC.md exists. Use when this capability is needed.
metadata:
  author: knishioka
---

# Resolve Issue Skill

Implements a feature from Issue specification through to PR draft.

## Purpose

Complete implementation workflow:
1. Parse Issue spec (Background/AC/Non-goals/Constraints)
2. Generate plan.md with clear scope (will/won't do)
3. Implement minimal changes
4. Run tests until passing
5. Generate PR_DRAFT.md with verification results

## Prerequisites

- Issue exists (GitHub) OR `ISSUE_WITH_SPEC.md` exists locally
- `make install` completed
- `make test` passes (baseline)

## Process

### Step 1: Read Issue Specification

Read from:
- GitHub Issue (if Issue number provided)
- `ISSUE_WITH_SPEC.md` (if exists locally)
- User provides Issue URL

Extract:
- Background/Why
- Acceptance Criteria
- Non-goals
- Constraints

### Step 2: Generate plan.md (REQUIRED GATE)

Create `plan.md` with:

```markdown
# Implementation Plan

## This PR Will Do

- [Specific file change 1]
- [Specific file change 2]
- [Max 5 items - concrete actions]

## This PR Will NOT Do

- [Item from Non-goals]
- [Future enhancement deferred]

## Files to Change

- `path/to/file1.py` - Add X function
- `path/to/file2.py` - Add tests for X

## Verification Commands

\`\`\`bash
make test
python -c "from module import func; print(func('input'))"
\`\`\`

## Risk

[1-2 sentences: What could go wrong]

## Rollback

[1-2 sentences: How to undo if needed]
```

**STOP HERE** - Show plan.md to user for approval before implementing.

### Step 3: Implement

Following plan.md:
- Make minimal changes (no refactoring)
- Add/update tests for all ACs
- Run `make test` until passing
- Fix issues, don't skip failures

### Step 4: Generate PR_DRAFT.md

Structure (matching CLAUDE.md template):

```markdown
## Summary

[1-2 sentences: What this PR does]

## Related Issue

Closes #[Issue number - user fills]

## Verification

### Automated Tests
- [x] `make test` - ✅ [N] passed in [X]s

### Manual Testing
[Only include if actually performed - NO fake checkmarks]
- [x] `command` - ✅ Result summary

## Notes

[Optional: Any caveats or follow-up items]
```

## Verification Rules (CRITICAL)

**Format**: `command` (executor) - ✅/❌ result

**Examples**:
- ✅ `make test` - ✅ 7 passed in 0.01s
- ✅ `python -c "..."` - ✅ Returns "expected"
- ❌ `make lint` - Unchecked (not performed)

**Rules**:
- Only check items actually executed
- Include command output summary
- No assumptions or "should work"
- If not done, don't check it

## Output Files

- `plan.md` - Scope definition (generated before implementation)
- `PR_DRAFT.md` - PR body with actual verification results

## Constraints

- **No unrelated changes** - Stick to plan.md scope
- **No refactoring** - Unless explicitly in AC
- **All tests must pass** - Don't generate PR_DRAFT.md with failing tests
- **Minimal diff** - Smallest change to satisfy ACs

## Example Workflow

```bash
# User has Issue #123 or ISSUE_WITH_SPEC.md

/resolve-issue

# Skill generates plan.md, shows user
# User: "Looks good, proceed"
# Skill implements, runs tests, generates PR_DRAFT.md
# User reviews PR_DRAFT.md and posts PR
```

## Error Handling

If tests fail:
1. Show error output
2. Fix the issue
3. Retry `make test`
4. Only generate PR_DRAFT.md when all tests pass

If stuck:
- Ask user for guidance
- Don't generate incomplete PR_DRAFT.md
- Document blocker in plan.md

## Notes

- This skill replaces the 3-step workflow (`/spec` → `/plan` → `/ship`)
- For learning the process, use individual skills instead
- plan.md acts as a **scope gate** - must be approved before coding
- PR_DRAFT.md is ready to paste into GitHub PR form

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/knishioka) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
