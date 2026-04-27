---
name: worker-protocol
description: Defines behavior protocol for spawned worker agents. Injected into worker prompts. Covers startup, progress reporting, exit conditions, and handover preparation. Use when this capability is needed.
metadata:
  author: troykelly
---

# Worker Protocol

## Overview

This skill defines the behavioral contract for spawned worker agents. It is injected into worker prompts and governs how workers operate.

**Core principle:** Workers are single-purpose, self-documenting, and gracefully exit.

**Note:** This skill is not invoked directly - it's embedded in worker prompts by `worker-dispatch`.

## Worker Identity

Every worker has:

| Property | Example | Purpose |
|----------|---------|---------|
| `worker_id` | `worker-1701523200-123` | Unique identifier |
| `issue` | `123` | Assigned issue number |
| `attempt` | `1` | Which attempt this is |
| `orchestration_id` | `orch-2025-12-02-001` | Parent orchestration |

## Startup Checklist

Workers MUST execute this checklist before starting work:

```markdown
## Worker Startup Checklist

1. [ ] Read assigned issue completely
2. [ ] Check issue comments for context/history
3. [ ] Verify on correct branch (`git branch --show-current`)
4. [ ] Check worktree is clean (`git status`)
5. [ ] Run existing tests to verify baseline (`pnpm test` or equivalent)
6. [ ] Post startup comment to issue
```

### Startup Comment Template

```markdown
🤖 **Worker Started**

| Property | Value |
|----------|-------|
| Worker ID | `[WORKER_ID]` |
| Attempt | [N] |
| Branch | `[BRANCH_NAME]` |
| Started | [TIMESTAMP] |

**Understanding:**
[1-2 sentence summary of what the issue requires]

**Approach:**
[Brief planned approach]

---
*Orchestration: [ORCHESTRATION_ID]*
```

## Progress Reporting

### When to Report

Workers post progress comments:

1. **On start** - Startup checklist complete
2. **On milestone** - Significant progress (e.g., "tests passing")
3. **On blocker** - When encountering impediment
4. **On completion** - PR created or handover needed

### Progress Comment Template

```markdown
🤖 **Worker Update**

**Status:** [Implementing|Testing|Blocked|Complete]
**Turns Used:** [N]/100

**Progress:**
- [x] [Completed item]
- [x] [Completed item]
- [ ] [In progress item]
- [ ] [Remaining item]

**Current:**
[What you're working on right now]

**Next:**
[What you'll do next]

---
*Worker: [WORKER_ID]*
```

## Exit Conditions

Workers exit when ANY of these conditions are met:

### 1. PR Created (Success)

```markdown
🤖 **Worker Complete** ✅

**PR Created:** #[PR_NUMBER]
**Issue:** #[ISSUE]
**Branch:** `[BRANCH]`

**Summary:**
[1-2 sentences describing what was implemented]

**Tests:**
- [N] tests passing
- Coverage: [X]%

**Ready for:** CI verification

---
*Worker: [WORKER_ID] | Turns: [N]/100*
```

### 2. Handover Needed (Turn Limit)

When approaching 100 turns (at ~85-90 turns):

```markdown
🤖 **Handover Required** 🔄

**Turns Used:** [N]/100
**Reason:** Approaching turn limit

**Handover file created:** `.orchestrator/handover-[ISSUE].md`

A replacement worker will continue this work with full context.

---
*Worker: [WORKER_ID]*
```

See `worker-handover` skill for handover file format.

### 3. Blocked (External Dependency)

Only after exhausting all options:

```markdown
🤖 **Worker Blocked** 🚫

**Reason:** [Clear description of blocker]

**What I tried:**
1. [Approach 1] - [Why it didn't work]
2. [Approach 2] - [Why it didn't work]

**Required to unblock:**
- [ ] [Specific action needed from human/external]

**Cannot proceed because:**
[Explanation of why this is a true blocker, not just a hard problem]

---
*Worker: [WORKER_ID] | Attempt: [N]*
```

### 4. Failed (Needs Research)

When implementation fails after good-faith effort:

```markdown
🤖 **Worker Failed - Research Needed** 🔬

**Failure:** [What failed]
**Attempt:** [N]

**What I tried:**
1. [Approach 1] - [Result]
2. [Approach 2] - [Result]

**Error/Issue:**
```
[Error output or description]
```

**Hypothesis:**
[What I think might be wrong]

**Research needed:**
- [ ] [Specific question to research]
- [ ] [Area to investigate]

---
*Worker: [WORKER_ID] | Triggering research cycle*
```

## Review Gate (MANDATORY)

**Before creating ANY PR, workers MUST:**

1. Complete `comprehensive-review` skill (7 criteria)
2. Post review artifact to issue comment (exact format required)
3. Address ALL findings using `apply-all-findings` skill
4. Verify "Unaddressed: 0" in artifact
5. Update artifact status to "COMPLETE"

**Review artifact format (machine-parseable):**

```markdown
<!-- REVIEW:START -->
## Code Review Complete

| Property | Value |
|----------|-------|
| Worker | `[WORKER_ID]` |
| Issue | #[ISSUE] |
| Reviewed | [TIMESTAMP] |

[... full artifact per comprehensive-review skill ...]

**Review Status:** ✅ COMPLETE
<!-- REVIEW:END -->
```

**CRITICAL:** The `PreToolUse` hook will BLOCK `gh pr create` if:
- No review artifact found in issue comments
- Review status is not COMPLETE
- Unaddressed findings > 0

### Security-Sensitive Changes

If worker modifies files matching security-sensitive patterns:
- `**/auth/**`, `**/api/**`, `**/middleware/**`
- `**/*password*`, `**/*token*`, `**/*secret*`

Worker MUST:
1. Invoke `security-review` skill OR run `codex-subagent security-reviewer`
2. Include security review in artifact
3. Mark "Security-Sensitive: YES" in artifact

## Behavioral Rules

### DO

- ✅ Work ONLY on assigned issue
- ✅ Follow all project skills (TDD, strict-typing, ipv6-first, etc.)
- ✅ Commit frequently with descriptive messages
- ✅ Update issue with progress comments
- ✅ Test thoroughly before creating PR
- ✅ Complete `comprehensive-review` before PR
- ✅ Post review artifact to issue
- ✅ Address ALL review findings (no exceptions)
- ✅ Prepare handover if approaching turn limit
- ✅ Exit cleanly when done

### DO NOT

- ❌ Start work on other issues
- ❌ Modify unrelated code
- ❌ Skip tests to save turns
- ❌ Create PR without passing tests
- ❌ Create PR without review artifact
- ❌ Leave review findings unaddressed
- ❌ Defer findings without tracking issues
- ❌ Ignore project standards
- ❌ Continue past 100 turns
- ❌ Delete or modify other workers' branches

## Commit Message Format

Workers use this commit format:

```
[type]: [description] (#[ISSUE])

[Body if needed]

🤖 Worker: [WORKER_ID]
```

Types:
- `feat`: New feature
- `fix`: Bug fix
- `test`: Test additions
- `refactor`: Code refactoring
- `docs`: Documentation

Example:
```
feat: Add dark mode toggle to settings (#123)

- Created ThemeContext with dark/light modes
- Added toggle switch to settings page
- Persisted preference to localStorage

🤖 Worker: worker-1701523200-123
```

## PR Creation

**PREREQUISITE:** Review artifact MUST be posted to issue before PR creation.

### Pre-PR Verification

```bash
# Verify review artifact exists in issue
REVIEW_EXISTS=$(gh api "/repos/$OWNER/$REPO/issues/$ISSUE/comments" \
  --jq '[.[] | select(.body | contains("<!-- REVIEW:START -->"))] | length')

if [ "$REVIEW_EXISTS" = "0" ]; then
  echo "ERROR: No review artifact found. Complete comprehensive-review first."
  exit 1
fi

# Verify review is COMPLETE with 0 unaddressed
REVIEW_STATUS=$(gh api "/repos/$OWNER/$REPO/issues/$ISSUE/comments" \
  --jq '[.[] | select(.body | contains("<!-- REVIEW:START -->"))] | last | .body' \
  | grep -o "Review Status:.*" | head -1)

echo "Review status: $REVIEW_STATUS"
```

### PR Title Format

```
[type]: [description] (#[ISSUE])
```

### PR Body Template

```markdown
## Summary
[1-2 sentence description]

Closes #[ISSUE]

## Changes
- [Change 1]
- [Change 2]
- [Change 3]

## Testing
- [ ] Unit tests added/updated
- [ ] Integration tests passing
- [ ] Manual testing completed

## Code Review
- [x] Review artifact posted to issue
- [x] All findings addressed
- [x] Review status: COMPLETE

## Checklist
- [ ] Code follows project style
- [ ] Types are complete (no `any`)
- [ ] Documentation updated
- [ ] IPv6-first verified (if applicable)

---
🤖 **Automated PR**
Worker: `[WORKER_ID]`
Orchestration: `[ORCHESTRATION_ID]`
Attempt: [N]
Review: See issue #[ISSUE] for review artifact
```

## Worktree Awareness

Workers operate in isolated worktrees and must:

1. **Stay in worktree** - Don't cd to main repo
2. **Use relative paths** - Worktree is the root
3. **Commit to branch** - Not main
4. **Push to remote** - For PR creation

```bash
# Verify worktree
git worktree list

# Verify branch
git branch --show-current  # Should be feature/[ISSUE]-*

# Verify not on main
if [ "$(git branch --show-current)" = "main" ]; then
  echo "ERROR: On main branch in worktree!"
  exit 1
fi
```

## Turn Awareness

Workers should be aware of their turn budget:

| Turns | Status | Action |
|-------|--------|--------|
| 0-50 | Early | Normal work |
| 50-80 | Mid | Monitor progress |
| 80-90 | Late | Prepare handover if needed |
| 90-100 | Critical | Finalize and handover |

At turn 85+:
```
⚠️ **Turn Check:** [N]/100 turns used

If work is not nearly complete, begin handover preparation now.
```

## Error Recovery

When errors occur:

1. **Don't panic** - Errors are information
2. **Document** - Capture error output
3. **Analyze** - Understand what went wrong
4. **Retry once** - Try an alternative approach
5. **Report** - If still failing, report for research

```markdown
🤖 **Error Encountered**

**Command:** `[command that failed]`
**Error:**
```
[error output]
```

**Analysis:** [What I think went wrong]
**Retry:** [What I'll try next]
```

## Handover Trigger

Workers MUST create handover when:

1. Turn count reaches 90+
2. Cannot complete in remaining turns
3. Blocked but context valuable for next attempt

See `worker-handover` skill for handover file format.

## Integration

This protocol is used by:
- All spawned workers

This protocol requires workers to follow:
- `issue-driven-development` - Core workflow
- `strict-typing` - Type requirements
- `ipv6-first` - Network requirements
- `tdd-full-coverage` - Testing approach
- `clean-commits` - Commit standards
- `worker-handover` - Handover format
- `comprehensive-review` - Code review (MANDATORY before PR)
- `apply-all-findings` - Address all findings
- `security-review` - For security-sensitive files
- `deferred-finding` - For tracking deferred findings
- `review-gate` - PR creation gate

This protocol is enforced by:
- `PreToolUse` hook on `gh pr create` - Blocks without review artifact
- `Stop` hook - Verifies review completion before session end

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/troykelly) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-12 -->
