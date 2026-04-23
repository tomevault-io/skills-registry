---
name: review-gitlab-epic
description: Review a completed epic's branch against every issue's acceptance criteria Use when this capability is needed.
metadata:
  author: capplequoppe
---

## GitLab Helper Scripts

Use these deterministic scripts instead of crafting curl commands:

| Script | Purpose |
|--------|---------|
| `.claude/hooks/gitlab/scripts/get-epic.sh <iid> --with-issues` | Fetch epic with all issues |
| `.claude/hooks/gitlab/scripts/create-issue.sh` | Create remediation issues |

All scripts support `--help` for usage details.

## Overview

This skill performs a post-completion audit of an epic. It reviews the **actual state of the code on the epic's branch** against **every issue's acceptance criteria and requirements**.

Any findings are filed as new GitLab issues attached to the same epic.

## Steps

### 1. Fetch epic and determine branch

```bash
.claude/hooks/gitlab/scripts/get-epic.sh $ARGUMENTS[0] --with-issues --format=json
```

Determine the branch to review based on labels:
- Has `execution-plan::{plan-name}` AND `phase::{phase-name}` → review `phase/{plan-name}/{phase-name}`
- No `execution-plan::*` label → review `epic/{epic-iid}-{epic-slug}`

### 2. Check out the branch

```bash
git fetch origin
git checkout origin/{branch}
```

### 3. Review each issue against the codebase

For each issue (in IID order):

#### a. Extract acceptance criteria

Parse the issue description to identify:
- Explicit acceptance criteria
- Implicit requirements
- Referenced files, structs, traits, functions

#### b. Locate the relevant code

Use Grep and Glob to find:
- New files/functions mentioned in the issue
- Modified files mentioned in the issue
- Related test files

#### c. Verify each criterion

For every criterion, classify as:
1. **Implemented** — Fully satisfied
2. **Partially implemented** — Some aspects incomplete
3. **Missing** — No evidence of implementation
4. **Broken/Regressed** — Code exists but doesn't satisfy criterion

Pay special attention to:
- Cross-task regressions
- Interface contract changes
- Missing tests
- Dead code

#### d. Record findings

For each finding, note:
- Original issue IID and title
- Specific criterion violated
- File(s) and line(s) involved
- Classification: `missing`, `broken`, `partial`, `deviation`
- What needs to be done to fix it

### 4. Present findings

Group findings by issue and show:
- Pass/fail status per issue
- List of findings (if any)
- Summary: total issues, issues with findings, total findings

If **zero findings**, report success and stop.

If findings exist, ask for confirmation before creating issues.

### 5. Create remediation issues

For each finding, create a new issue:

```bash
.claude/hooks/gitlab/scripts/create-issue.sh \
  --title="[Audit] {classification}: {short description} (from #{original-iid})" \
  --description="## Context

This issue was identified during a post-completion audit of epic **{epic-title}** (&{epic-iid}).

**Original issue**: #{original-iid} — {original-title}

## Finding

**Classification**: \`{classification}\`

**Acceptance criterion**:
> {the specific criterion}

**Current state**:
{description of current state}

**Files involved**:
- \`{file}:{line}\` — {note}

## Expected resolution

{what needs to be done}" \
  --labels="{original-labels},audit" \
  --epic-iid={epic-iid}
```

### 6. Report

Print final summary:
- Number of issues reviewed
- Number of findings
- List of newly created issues with URLs
- Epic URL for reference

## Notes

- This skill uses a **forked context** and the **code-reviewer agent**
- Review is performed against the branch as-is, not individual diffs
- Focus on requirements that are not met, not stylistic issues
- Skip intentional, documented deviations

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/capplequoppe) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
