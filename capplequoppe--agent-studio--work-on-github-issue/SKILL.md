---
name: work-on-github-issue
description: Work on an issue from GitHub Use when this capability is needed.
metadata:
  author: capplequoppe
---

## Steps

### 1. Fetch issue details

Use the `gh` CLI to get the full details of issue `$ARGUMENTS[0]`:
```
gh issue view $ARGUMENTS[0] --json number,title,body,labels,milestone
```

Retrieve:
- Issue number, title, description, and labels
- Associated milestone (if any) — this is the GitHub equivalent of a GitLab epic

### 2. Determine branching context

Inspect the issue's labels and milestone association to determine which of the three branching strategies applies:

#### Case A: Multi-phase execution plan

**Detection**: The issue has a label matching `execution-plan::*` AND a label matching `phase::*`.

Extract:
- `{plan-name}` from the `execution-plan::{plan-name}` label
- `{phase-name}` from the `phase::{phase-name}` label

Branching:
- **Root branch**: `plan/{plan-name}` (branches off `main`)
- **Phase branch**: `phase/{plan-name}/{phase-name}` (branches off root branch)
- **Issue branch**: `feat/{issue-number}-{issue-title-in-kebab-case}` (branches off phase branch)
- **PR target**: the phase branch (`phase/{plan-name}/{phase-name}`)

#### Case B: Single milestone (no execution plan)

**Detection**: The issue is assigned to a milestone but does NOT have an `execution-plan::*` label.

Extract:
- `{milestone-slug}` from the milestone title, kebab-cased
- `{milestone-number}` from the milestone

Branching:
- **Milestone branch**: `milestone/{milestone-number}-{milestone-slug}` (branches off `main`)
- **Issue branch**: `feat/{issue-number}-{issue-title-in-kebab-case}` (branches off milestone branch)
- **PR target**: the milestone branch (`milestone/{milestone-number}-{milestone-slug}`)

#### Case C: Standalone issue (no milestone)

**Detection**: The issue has no associated milestone.

Branching:
- **Issue branch**: `feat/{issue-number}-{issue-title-in-kebab-case}` (branches off `main`)
- **PR target**: `main`

### 3. Check out or create branches

1. Fetch latest from origin.
2. Check if the issue already has a linked PR:
   ```
   gh pr list --search "head:feat/{issue-number}-" --json number,headRefName
   ```
   If found, check out that PR's branch and skip to step 4.
3. Otherwise, ensure the **base branch** exists:
   - For Case A: create `plan/{plan-name}` from `main` if it doesn't exist, then create `phase/{plan-name}/{phase-name}` from the plan branch if it doesn't exist. Push both to origin if newly created.
   - For Case B: create `milestone/{milestone-number}-{milestone-slug}` from `main` if it doesn't exist. Push to origin if newly created.
   - For Case C: no base branch to create.
4. Create the issue branch from the appropriate base branch and check it out.

### 4. Analyze requirements

- Read the issue description carefully and extract acceptance criteria.
- If the issue belongs to a milestone, fetch the milestone description for context:
  ```
  gh api repos/{owner}/{repo}/milestones/{number}
  ```
- Explore the codebase as needed to understand how to implement the solution.

### 5. Interview the user

Ask clarifying questions about requirements, edge cases, or implementation choices before writing code.

### 6. Implement the solution

Make code changes in the relevant files while following Clean Code Best Practices and SOLID Principles.

### 7. Test

After implementing the solution, run the tests related to the issue using a subagent to ensure that everything is working correctly.

### 8. Debug if needed

If the tests fail, debug the code changes and fix any issues until all tests pass.

### 9. Code review

Review the code changes made for the issue to ensure code quality and adherence to best practices.

### 10. Commit

If the tests pass, prepare a commit message that summarizes the changes made for the issue. Do NOT push without user approval.

### 11. Create a pull request

Create a pull request for the issue if none already exists. The PR base branch must match the branching strategy determined in step 2:
- Case A: PR into `phase/{plan-name}/{phase-name}`
- Case B: PR into `milestone/{milestone-number}-{milestone-slug}`
- Case C: PR into `main`

```
gh pr create --base {target-branch} --title "..." --body "Closes #{issue-number}\n\n{summary}"
```

Do NOT merge without user approval.

## Branch naming reference

```
Case A — Multi-phase execution plan:

  main
    └── plan/{plan-name}
          ├── phase/{plan-name}/{phase-name-1}
          │     ├── feat/{number}-{slug}
          │     └── feat/{number}-{slug}
          └── phase/{plan-name}/{phase-name-2}
                └── feat/{number}-{slug}

Case B — Single milestone:

  main
    └── milestone/{milestone-number}-{milestone-slug}
          ├── feat/{number}-{slug}
          └── feat/{number}-{slug}

Case C — Standalone issue:

  main
    └── feat/{number}-{slug}
```

## Notes

- The `{issue-title-in-kebab-case}` should be truncated to a reasonable length (max ~50 chars) to avoid excessively long branch names.
- When creating base branches (plan, phase, milestone), always check if they already exist on origin first to avoid conflicts with work from other contributors.
- This skill only creates PRs for individual issues into their immediate parent branch. Merging phase branches into the plan branch, or the plan branch into main, is handled by the `merge-completed-work-github` skill.
- GitHub uses `main` as the default branch (not `master`). Verify with `gh repo view --json defaultBranchRef` if uncertain.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/capplequoppe) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
