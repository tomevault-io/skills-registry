---
name: github-issue-planner
description: > Use when this capability is needed.
metadata:
  author: jumptwentyfour
---

# GitHub Issue Planner

Plan issues collaboratively, then create them in GitHub using `gh` CLI.

## Workflow

### 1. Determine Issue Type

Ask if not obvious from context:
- **Bug** - Something broken or not working as expected
- **Feature** - New functionality or enhancement
- **Task** - Internal task or chore
- **Epic** - Large initiative requiring multiple child issues

Always assign an issue type to the issue, an issue should always have one. Set via the GitHub GraphQL API after creating the issue (the --type flag is not reliably supported).

#### Big Task Detection

Before creating an issue, evaluate whether the request is a "big task" that should be an Epic with subtasks. A task qualifies as big if it has:

- **Multiple distinct components** - The work involves several separate features or pieces
- **Cross-cutting concerns** - Requires work across different areas (frontend, backend, database)
- **Extended scope** - Would take more than a few days to complete
- **Multiple acceptance criteria** - Has several user stories or distinct outcomes
- **System-wide impact** - Affects multiple parts of the codebase

When a big task is detected, follow the Epic Creation Workflow below instead of creating a single issue.

### 2. Gather Requirements

Ask targeted questions based on type:

**Bug:**
- What's happening vs what should happen?
- Steps to reproduce?
- Environment details?

**Feature:**
- Who is this for and what problem does it solve?
- What does "done" look like?
- Any constraints or dependencies?

**Task**
- What needs to be done?
- What does "done" look like?

**Epic:**
- What's the high-level goal?
- What are the major milestones?
- What's explicitly out of scope?

Keep questions minimal—gather just enough to write a clear issue.

### 3. Draft the Issue

Use templates from [references/templates.md](references/templates.md).

Present the draft to the user:
```
Here's the draft issue:

**Title:** [title]

[body content]

---
Ready to create this in GitHub?
```

### 4. Create in GitHub

After user confirms, create with `gh`:

```bash
gh issue create --title "Title here" --body "Body here"
```

For epics, create the parent issue first, then offer to create child issues that reference it.

#### Title Standards
- **Sentence case** - Capitalise only the first word and proper nouns.
- **No type prefixes** - Use GitHub issue types, not Bug:, Feature: etc...
- **Imperative mood for enhancements** - "Fix N+1 issue" not "Fixing N+1 issue"
- **Descriptive for bugs** - Describe the symptom "Failed to allocate booking to staff member"
- **Specific** - "Be specific, it must be understandable without opening the issue body"

## Notes

- Always show the draft before creating
- For epics, confirm which child issues to create
- If `gh` isn't authenticated, tell user to run `gh auth login`
- Never include "Generated with Claude Code"
- Never use title case for descriptions - use sentence case
- **Always follow the Label Management Process** - See [references/labels.md](references/labels.md) for available labels and the process to check/create/apply them
- Map priority to urgency labels: P1=Critical, P2=High, P3=Medium, P4=Low
- **Always apply the issue type to the issue**

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jumptwentyfour) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
