---
name: merge-completed-work-github
description: Merge a completed phase, milestone, or plan branch into its parent branch on GitHub Use when this capability is needed.
metadata:
  author: capplequoppe
---

## Overview

This skill handles "upward merges" in the branching hierarchy established by the `work-on-github-issue` skill.
It creates a pull request to merge a completed child branch into its parent:

- **Phase branch → plan branch** (when all issues in a phase milestone are done)
- **Milestone branch → main** (when all issues in a standalone milestone are done)
- **Plan branch → main** (when all phases of an execution plan are done)

## Argument

The argument `$ARGUMENTS[0]` is interpreted as follows:

- **Numeric** → treated as a **milestone number** (for phase completion or single-milestone completion)
- **Non-numeric** → treated as a **plan name** (for plan-to-main completion)

## Steps

### 1. Identify the repository

Determine the repository from `git remote get-url origin` (extract `{owner}/{repo}`).
Verify the default branch name:
```
gh repo view --json defaultBranchRef -q .defaultBranchRef.name
```

### 2. Determine merge type

#### If argument is numeric (milestone number):

Fetch the milestone:
```
gh api repos/{owner}/{repo}/milestones/$ARGUMENTS[0]
```

List the milestone's issues to inspect labels:
```
gh issue list --milestone "{milestone-title}" --json number,labels --limit 1
```

- **Has `execution-plan::{plan-name}` AND `phase::{phase-name}`** → Phase completion
  - Source branch: `phase/{plan-name}/{phase-name}`
  - Target branch: `plan/{plan-name}`
- **No `execution-plan::*` label** → Single-milestone completion
  - Derive `{milestone-slug}` from the milestone title, kebab-cased
  - Source branch: `milestone/{milestone-number}-{milestone-slug}`
  - Target branch: `main`

#### If argument is non-numeric (plan name):

This is a plan completion.
- Source branch: `plan/{plan-name}`
- Target branch: `main`

### 3. Verify readiness

#### For milestone-based merges (phase or single-milestone):

1. List all issues in the milestone:
   ```
   gh issue list --milestone "{milestone-title}" --state all --json number,state --limit 200
   ```
2. Check that **every issue is closed** (`state == "closed"`)
3. If any issues are still open, report them to the user and **abort**

#### For plan completion:

1. Find all milestones with issues labeled `execution-plan::{plan-name}`:
   ```
   gh issue list --label "execution-plan::{plan-name}" --state all --json number,state,milestone --limit 500
   ```
2. Group by milestone and verify all issues in every phase are closed
3. Verify all phase branches have been merged into the plan branch (check for existing merged PRs or compare branch HEADs)
4. If any phase has open issues or an unmerged branch, report and **abort**

### 4. Verify branch exists

1. `git fetch origin`
2. Confirm the source branch exists:
   ```
   git ls-remote --heads origin {source-branch}
   ```
3. If not, report and **abort**

### 5. Check for open PRs targeting the source branch

```
gh pr list --base {source-branch} --state open --json number,title
```

If there are open PRs targeting the source branch (unmerged issue branches), report them and **abort**.

### 6. Present merge summary to the user

Before creating the PR, present:
- Source branch and target branch
- Number of commits being merged: `git log origin/{target}..origin/{source} --oneline`
- List of issues being delivered (from the milestone)
- Ask for user confirmation before proceeding

### 7. Create the pull request

```
gh pr create \
  --base {target-branch} \
  --head {source-branch} \
  --title "{pr-title}" \
  --body "{pr-body}"
```

**PR title conventions**:
- Phase completion: `Merge {phase-name} into {plan-name}`
- Milestone completion: `Merge milestone #{milestone-number}: {milestone-title}`
- Plan completion: `Merge plan: {plan-name}`

**PR body** should include:
- A summary section listing all issues delivered (with `#{number}` references so GitHub auto-links them)
- For phase merges: a link to the milestone
- For plan merges: links to all phase milestones

To enable auto-deletion of the source branch after merge, use:
```
gh pr merge {pr-number} --delete-branch --auto
```
But do NOT actually merge — just set the auto-delete flag if the user approves.

Do NOT merge without user approval.

### 8. Report

Print:
- The PR URL
- Source → target branch
- Number of issues delivered
- Reminder that the user must approve and merge manually

## Merge hierarchy reference

```
plan/{plan-name} ──────────────────────────────► main
  │                                                ▲
  │  (this skill: plan completion)                 │
  │                                                │
  ├── phase/{plan-name}/{phase-1} ─────► plan/     │
  │     (this skill: phase completion)             │
  │                                                │
  └── phase/{plan-name}/{phase-2} ─────► plan/     │
                                                   │
milestone/{num}-{slug} ───────────────────────────►│
      (this skill: milestone completion)
```

## Notes

- This skill only creates PRs — it does not merge, rebase, or modify local branches.
- GitHub automatically deletes branches after merge if configured on the repository (`Settings > General > Automatically delete head branches`). The `--delete-branch` flag on `gh pr merge` also handles this.
- If the source branch has conflicts with the target, the PR will still be created but GitHub will flag it as unmergeable. The user must resolve conflicts before merging.
- GitHub uses `main` as the default branch. Always verify with `gh repo view --json defaultBranchRef` rather than hardcoding.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/capplequoppe) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
