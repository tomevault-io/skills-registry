---
name: publish-execution-plan-github
description: Publish a local execution plan to GitHub as milestones and issues Use when this capability is needed.
metadata:
  author: capplequoppe
---

## Overview

This skill publishes a local execution plan (structured according to the `execution-plans` skill)
to GitHub as a set of milestones and issues. Each phase becomes a milestone, with task markdown
files becoming issues assigned to that milestone. Labels tie everything together.

GitHub does not have epics. Milestones serve as the phase-level grouping mechanism, and the
`execution-plan::{plan-name}` label acts as the cross-milestone "virtual epic" for filtering.

## Prerequisites

- The execution plan directory must follow the structure defined by the `execution-plans` skill:
  - A root `README.md` describing the plan as a whole
  - Phase subdirectories (named `phase-*`) each containing a `README.md` and task markdown files
  - Task markdown files structured as issues with Title, Description, Acceptance Criteria, etc.
- The repository can be identified by `git remote get-url origin`
- The `gh` CLI must be authenticated (`gh auth status`)

## Steps

### 1. Validate the execution plan directory

At the path given in `$ARGUMENTS[0]`:
- Confirm the root `README.md` exists
- Identify all phase subdirectories (sorted alphanumerically)
- For each phase, identify all task markdown files (sorted alphanumerically, excluding `README.md`)

### 2. Determine GitHub repository

- Extract `{owner}/{repo}` from `git remote get-url origin`
- Verify access: `gh repo view {owner}/{repo} --json name`
- Confirm with the user before proceeding

### 3. Create labels

Derive the plan name from the execution plan directory name (e.g., `reorg-validation`).

Create labels if they don't already exist:
- `execution-plan::{plan-name}` (distinct color, e.g., `#0033CC`)
- `phase::{phase-directory-name}` for each phase (e.g., `#6699CC`)

```
gh label create "execution-plan::{plan-name}" --color "0033CC" --description "Execution plan: {plan-name}" --force
gh label create "phase::{phase-name}" --color "6699CC" --force
```

The `--force` flag updates the label if it already exists.

### 4. Read the root README.md

This content will be included in every milestone description for global context.

### 5. Create milestones and issues

For each phase subdirectory (in order):

#### a. Read the phase README.md

#### b. Create a GitHub milestone

```
gh api repos/{owner}/{repo}/milestones --method POST \
  -f title="[{plan-name}] {phase-directory-name}: {title from phase README H1}" \
  -f description="{milestone-description}"
```

**Milestone description** composed as:
```markdown
> This milestone is part of the **{plan-name}** execution plan.
> See related milestones by filtering issues with label `execution-plan::{plan-name}`.

---

<details>
<summary>Execution Plan Context (click to expand)</summary>

{root README.md content}

</details>

---

{phase README.md content}
```

Record the milestone number for issue assignment.

#### c. Create issues for each task

For each task markdown file in the phase (sorted, excluding README.md):
- Read the task file content
- Extract the title from the first H1 heading (line starting with `# `)
- Extract the description (everything after the H1 line)
- Create the issue:
  ```
  gh issue create \
    --title "{extracted H1 title}" \
    --body "{description}" \
    --label "execution-plan::{plan-name}" \
    --label "phase::{phase-directory-name}" \
    --milestone "{milestone-title}"
  ```
- Record the issue number and title for dependency linking

### 6. Rewrite local file links in milestone descriptions (second pass)

After all milestones and issues are created, build a mapping:
- Phase README paths → milestone URLs (e.g., `./phase-1-domain-foundation/README.md` → milestone URL)
- Task file paths → issue URLs (e.g., `./01-value-object-tests.md` → issue URL)

For each milestone, update its description:
```
gh api repos/{owner}/{repo}/milestones/{number} --method PATCH \
  -f description="{updated-description}"
```

Replace all local markdown links `[text](./path.md)` with the corresponding GitHub URLs.

### 7. Link dependencies between issues (third pass)

- Parse the root `README.md` for phase-level dependencies
- For each phase README, check the "Dependencies" section
- GitHub does not have native issue link types like `is_blocked_by`. Instead, use one of:
  - **Tasklist references** in a tracking issue: `- [ ] #{number}`
  - **Cross-references** in issue bodies: add a "Blocked by #{number}" line to the issue description
  - **Sub-issues** (if enabled): `gh api repos/{owner}/{repo}/issues/{number}/sub_issues --method POST -F sub_issue_id={id}`
- At minimum, add a comment on the **first issue of each phase** referencing the **last issue of its predecessor phase**:
  ```
  gh issue comment {number} --body "Blocked by #{predecessor-last-issue-number} (previous phase)"
  ```

### 8. Print a summary

- List of milestones with their URLs
- List of issues per milestone with their URLs
- List of dependency links created
- The shared label for filtering: `execution-plan::{plan-name}`

## GitHub CLI Reference

### Milestones
- Create: `gh api repos/{owner}/{repo}/milestones --method POST -f title="..." -f description="..."`
- Update: `gh api repos/{owner}/{repo}/milestones/{number} --method PATCH -f description="..."`
- List: `gh api repos/{owner}/{repo}/milestones`

### Issues
- Create: `gh issue create --title "..." --body "..." --label "..." --milestone "..."`
- Comment: `gh issue comment {number} --body "..."`
- List by milestone: `gh issue list --milestone "..." --json number,title`

### Labels
- Create/update: `gh label create "{name}" --color "{hex}" --force`

## Error Handling

- If a milestone or issue creation fails, log the error and continue
- After all items are created, report any failures for manual remediation
- Rate limit: `gh` CLI handles rate limiting automatically with backoff

## Notes

- GitHub milestones are the closest equivalent to GitLab epics for phase grouping. They lack labels/scoped filtering on the milestone itself, but issues within them carry the labels.
- GitHub milestone descriptions support markdown, including `<details>` blocks.
- The `execution-plan::{plan-name}` label is the primary cross-milestone discovery mechanism — filtering by this label shows all issues across all phases.
- Unlike GitLab, GitHub does not have native `is_blocked_by` issue links. Dependency tracking relies on cross-references, comments, or sub-issues (beta feature).

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/capplequoppe) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
