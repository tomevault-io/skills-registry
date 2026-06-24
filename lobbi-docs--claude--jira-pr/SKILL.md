---
name: jirapr
description: Create pull request linked to Jira issue. Use when the user wants to "create PR", "open pull request", "submit for review", or "jira pr". Use when this capability is needed.
metadata:
  author: lobbi-docs
---

# Jira Pull Request Creation

Create a pull request linked to a Jira issue with proper formatting and linking.

## Usage

```
/jira:pr <issue-key>
```

## Features

- Creates GitHub PR
- Links PR to Jira issue
- Adds Jira issue key to title
- Generates comprehensive description
- Requests reviewers
- Transitions issue to "In Review"

## Related Commands

- `/jira:commit` - Create smart commit first
- `/jira:review` - Request code review

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/lobbi-docs) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
