---
name: jiracommit
description: Create smart commit linked to Jira issue. Use when the user wants to "commit", "smart commit", "jira commit", or "commit changes". Use when this capability is needed.
metadata:
  author: lobbi-docs
---

# Jira Smart Commit

Create a git commit linked to a Jira issue with smart commit syntax.

## Usage

```
/jira:commit [issue-key] [message]
```

## Features

- Formats commit with issue key
- Adds work log via smart commit
- Comments on issue
- Can transition issue status
- Links commit to issue

## Smart Commit Syntax

```
[ISSUE-KEY] message #comment text #time 2h #transition "In Progress"
```

## Related Commands

- `/jira:pr` - Create pull request
- `/jira:branch` - Create linked branch

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/lobbi-docs) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
