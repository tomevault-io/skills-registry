---
name: review-pr
description: Review pull requests against task specifications. Use when user wants a code review, PR review, or says "review PR", "review this PR", "check my PR", "code review", "review changes", "is this ready to merge". Reads the task spec and branch diff, verifies acceptance criteria are met, checks test coverage, identifies issues, and produces a structured verdict (approve/request changes). Use when this capability is needed.
metadata:
  author: porkbutts
---

# PR Review

Review a pull request by diffing the branch against its base, verifying acceptance criteria from the task spec, and producing a structured verdict.

## Workflow

```
START
  │
  ▼
┌──────────────────────────────┐
│ 1. Identify PR & Task        │ ◄── PR number/branch + task spec
└──────────┬───────────────────┘
           │
           ▼
┌──────────────────────────────┐
│ 2. Gather Diff & Context     │ ◄── git diff, file list, commit log
└──────────┬───────────────────┘
           │
           ▼
┌──────────────────────────────┐
│ 3. Acceptance Criteria Audit │ ◄── Check each criterion against diff
└──────────┬───────────────────┘
           │
           ▼
┌──────────────────────────────┐
│ 4. Test Coverage Audit       │ ◄── Identify missing/weak tests
└──────────┬───────────────────┘
           │
           ▼
┌──────────────────────────────┐
│ 5. Code Quality Review       │ ◄── Patterns, bugs, style, security
└──────────┬───────────────────┘
           │
           ▼
┌──────────────────────────────┐
│ 6. Generate Verdict          │ ◄── APPROVE / REQUEST CHANGES
└──────────────────────────────┘
```

## Step 1: Identify PR & Task

Determine what to review:

| Input | How to Resolve |
|-------|----------------|
| PR number | `gh pr view <number> --json headRefName,baseRefName,title,body,files` |
| Branch name | `git log <base>...<branch>`, infer PR if one exists |
| "Review this" (on a branch) | Use current branch, diff against main/master |

Find the task spec:

- Check the PR body for a task reference (e.g., `docs/tasks/task-3.1.2.md`)
- Infer from branch name (e.g., `feature/task-3.1.2-login-form` → `docs/tasks/task-3.1.2.md`)
- If no individual task doc, fall back to the matching section in `docs/TASKS.md`
- If no task spec found, ask the user — but still proceed with a general review if they don't have one

Read the task spec and extract:
- **Acceptance criteria** — the checklist to verify
- **Files expected to change** — compare against actual diff
- **Testing instructions** — what should be covered

## Step 2: Gather Diff & Context

Collect everything needed for review:

```bash
# Full diff against base branch
git diff <base>...<branch>

# List of changed files
git diff --name-status <base>...<branch>

# Commit history on the branch
git log --oneline <base>...<branch>
```

Read each changed file in full to understand context around the diff (not just the diff hunks). Also read:

- `docs/ARCHITECTURE.md` — to verify changes follow architectural patterns
- Existing test files adjacent to changed code — to understand testing conventions

## Step 3: Acceptance Criteria Audit

For each acceptance criterion from the task spec, determine:

| Status | Meaning |
|--------|---------|
| **MET** | Diff clearly implements this criterion |
| **PARTIAL** | Some aspects implemented, others missing or incomplete |
| **NOT MET** | No evidence of this criterion in the diff |
| **UNCLEAR** | Cannot determine from code alone (needs manual/visual QA) |

For each criterion, cite the specific file and code that satisfies it (or explain what's missing).

## Step 4: Test Coverage Audit

Review the test files in the diff. Check for:

**Coverage gaps:**
- Acceptance criteria with no corresponding test
- Happy path tested but error/edge cases missing
- New branches (if/else, switch, try/catch) without test coverage
- New public functions/methods without tests

**Test quality:**
- Tests that only assert `toBeDefined()` or similar weak assertions
- Tests that mock so heavily they don't test real behavior
- Missing setup/teardown that could cause test pollution
- Tests that would pass even if the implementation were wrong

If coverage reports are available (`--coverage` output), reference actual coverage numbers. Otherwise assess from reading the test code.

## Step 5: Code Quality Review

Scan for issues across these categories:

**Correctness:**
- Off-by-one errors, null/undefined handling, race conditions
- Missing error handling at system boundaries (API calls, file I/O, user input)
- Logic that doesn't match the task spec's described behavior

**Security (OWASP top 10):**
- Unsanitized user input (XSS, injection)
- Secrets or credentials in code
- Missing auth/authorization checks

**Patterns & conventions:**
- Deviations from `docs/ARCHITECTURE.md` (wrong directory, wrong abstraction)
- Inconsistency with existing code conventions in the repo
- Dead code, unused imports, leftover debug statements

**Scope:**
- Changes to files not in the task spec (intentional or accidental?)
- Unrelated refactors mixed into the PR
- Over-engineering beyond what the task requires

Keep findings actionable. Don't nitpick style issues that a linter would catch.

## Step 6: Generate Verdict

Produce a structured review report:

```markdown
# PR Review: <PR title>

**Branch:** `<branch>` → `<base>`
**Task:** <task ID and title>
**Verdict:** APPROVE | REQUEST CHANGES

## Acceptance Criteria

| # | Criterion | Status | Evidence |
|---|-----------|--------|----------|
| 1 | <criterion> | MET / PARTIAL / NOT MET / UNCLEAR | <file:line or explanation> |
| 2 | <criterion> | ... | ... |

**Summary:** X/Y criteria met.

## Test Coverage

### Missing Tests
- <what's not tested and should be>

### Weak Tests
- <tests that exist but don't assert meaningful behavior>

### Test Quality Notes
- <any positive observations or general notes>

## Code Review Findings

### Blocking (must fix)
- **[file:line]** <description of issue>

### Non-blocking (suggestions)
- **[file:line]** <description of suggestion>

### Positive Notes
- <things done well worth calling out>

## Scope Check
- **Expected files:** <from task spec>
- **Actual changed files:** <from diff>
- **Unexpected changes:** <any files changed outside task scope, or "None">

## Manual QA Checklist
- [ ] <visual/interactive checks from task spec that can't be verified from code>
```

### Verdict Rules

**APPROVE** when:
- All acceptance criteria are MET (UNCLEAR is acceptable if it requires visual QA)
- No blocking code review findings
- Tests exist for all criteria with reasonable coverage

**REQUEST CHANGES** when:
- Any acceptance criterion is NOT MET or PARTIAL
- Blocking code quality issues found
- Critical test coverage gaps (acceptance criteria with zero tests)

Always explain the verdict in one sentence after the status.

## Step 7: Post Review to PR

After generating the verdict, post it as a comment on the PR:

```bash
gh pr comment <number> --body "<review report>"
```

- Use a HEREDOC to pass the report body to avoid shell escaping issues
- If the PR number is not known (e.g., reviewing a branch with no open PR), skip this step and just output the report
- Do NOT use `gh pr review` — use `gh pr comment` so the comment is always visible regardless of verdict

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/porkbutts) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
