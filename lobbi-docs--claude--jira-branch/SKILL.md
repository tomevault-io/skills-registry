---
name: jirabranch
description: Create git branch linked to Jira issue. Use when the user wants to "create branch", "new branch for issue", "jira branch", or "feature branch". Use when this capability is needed.
metadata:
  author: lobbi-docs
---

# Jira Branch Creation

Create a git branch with naming convention linked to a Jira issue.

## Usage

```
/jira:branch <issue-key>
```

## Branch Naming

Format: `[type]/[issue-key]-[short-description]`

Examples:
- `feature/PROJ-123-user-authentication`
- `bugfix/PROJ-456-login-timeout`
- `hotfix/PROJ-789-security-patch`

## Features

- Creates properly named branch
- Links branch to Jira
- Sets up tracking
- Checks out branch

## Related Commands

- `/jira:work` - Start working on issue
- `/jira:commit` - Commit to branch

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/lobbi-docs) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
