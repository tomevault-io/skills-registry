---
name: review-github-milestone
description: Review a completed milestone's branch against every issue's acceptance criteria Use when this capability is needed.
metadata:
  author: capplequoppe
---

## Overview

This skill performs a post-completion audit of a GitHub milestone (the equivalent of a GitLab
epic). It reviews the **actual state of the code on the milestone's branch** against **every
issue's acceptance criteria and requirements**. The goal is to catch regressions, deviations, and
gaps caused by later tasks inadvertently breaking or deviating from earlier task requirements.

Any findings are filed as new GitHub issues assigned to the same milestone.

## Steps

### 1. Fetch milestone and determine branch

Determine the repository from `git remote get-url origin` (extract `{owner}/{repo}`).

Fetch the milestone details:
```
gh api repos/{owner}/{repo}/milestones/$ARGUMENTS[0]
```

Determine the branch to review. List milestone issues and inspect their labels:
```
gh issue list --milestone "{milestone-title}" --json number,labels --limit 200
```

- **Has `execution-plan::{plan-name}` AND `phase::{phase-name}`** → review branch `phase/{plan-name}/{phase-name}`
- **No `execution-plan::*` label** → review branch `milestone/{milestone-number}-{milestone-slug}`

### 2. Fetch all issues in the milestone

Retrieve all issues assigned to the milestone:
```
gh issue list --milestone "{milestone-title}" --state all --json number,title,body,labels --limit 200
```

Sort them by number ascending (original task order). Store each issue's:
- Number and title
- Full description (requirements, acceptance criteria, implementation notes)
- Labels

### 3. Check out the branch

1. `git fetch origin`
2. Check out the milestone/phase branch: `git checkout origin/{branch}`
3. Confirm the branch exists; if not, report and abort

### 4. Review each issue against the codebase

For **each issue** (in order), perform the following:

#### a. Extract acceptance criteria

Parse the issue description to identify:
- Explicit acceptance criteria (sections titled "Acceptance Criteria", "Requirements", "Definition of Done", or bullet lists under these headings)
- Implicit requirements from the description body (e.g., "must validate X", "should return Y")
- Referenced files, structs, traits, functions, or modules mentioned in the issue

#### b. Locate the relevant code

Using the file references from the issue and the codebase, find the code that should implement the issue's requirements. Use Grep and Glob to locate:
- New files, structs, traits, or functions mentioned in the issue
- Modified files mentioned in the issue
- Test files related to the issue

#### c. Verify each acceptance criterion

For every criterion, check whether:
1. **Implemented** — The criterion is fully satisfied by the current code
2. **Partially implemented** — Some aspects are present but incomplete
3. **Missing** — No evidence of implementation
4. **Broken/Regressed** — Code exists but does not satisfy the criterion (e.g., wrong behavior, removed by a later task, signature changed)

Pay special attention to:
- **Cross-task regressions**: A later task's refactor may have renamed, removed, or changed the behavior of something an earlier task introduced
- **Interface contract changes**: Trait signatures, function parameters, or return types that drifted from what the issue specified
- **Missing tests**: Acceptance criteria that require tests but have none
- **Dead code**: Code that was introduced for a task but is no longer reachable

#### d. Record findings

For each issue, produce a list of findings. Each finding should include:
- The original issue number and title it relates to
- The specific acceptance criterion that is violated or missing
- The file(s) and line(s) involved
- A classification: `missing`, `broken`, `partial`, or `deviation`
- A description of what needs to be done to fix it

### 5. Present findings to the user

After reviewing all issues, present a consolidated report:

- Group findings by issue
- For each issue, show: pass/fail status and the list of findings (if any)
- Show a summary: total issues reviewed, issues with findings, total findings count

If there are **zero findings**, report that the milestone passes the audit and stop.

If there are findings, ask the user for confirmation before creating issues.

### 6. Create remediation issues

For each finding (or group of related findings for the same original issue), create a new GitHub
issue:

```
gh issue create \
  --title "[Audit] {classification}: {short description} (from #{original-issue-number})" \
  --body "..." \
  --milestone "{milestone-title}" \
  --label "audit" --label "{execution-plan::*}" --label "{phase::*}"
```

**Issue body**:
```markdown
## Context

This issue was identified during a post-completion audit of milestone **{milestone-title}**.

**Original issue**: #{original-issue-number} — {original-issue-title}

## Finding

**Classification**: `{missing|broken|partial|deviation}`

**Acceptance criterion**:
> {the specific criterion from the original issue}

**Current state**:
{description of what the code currently does or doesn't do}

**Files involved**:
- `{file}:{line}` — {brief note}

## Expected resolution

{what needs to be done to satisfy the original criterion}
```

### 7. Report

Print a final summary:
- Number of issues reviewed
- Number of findings
- List of newly created issues with their URLs
- The milestone URL for reference

## Notes

- This skill uses a **forked context** and the **code-reviewer agent** to avoid polluting the main conversation with the full review.
- The review is performed against the branch as-is, not against diffs. This is intentional — the goal is to verify the final state, not individual changes.
- Findings should be actionable. Avoid stylistic or cosmetic observations — focus on requirements that are not met.
- If a deviation is intentional and documented (e.g., a task's "Additional Notes" explains why it differs from the original plan), it should not be flagged.
- For large milestones, the review may take significant time. The skill should process issues sequentially and report progress.
- GitHub milestones serve as the equivalent of GitLab epics in this workflow.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/capplequoppe) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
