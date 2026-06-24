---
name: jira
description: Access to Atlassian Jira with `jira` CLI. Use when this capability is needed.
metadata:
  author: vrslev
---

# Jira

Interact with Jira issues, projects, sprints, and boards using `jira` CLI.

## Search & View

```bash
jira issue list --plain --columns key,summary,status,assignee
jira issue list --jql 'project = PROJ AND status = "In Progress"' --plain
jira issue view PROJ-123 --plain --comments 5
```

## Projects & Sprints

```bash
jira project list --plain --columns key,name,type
jira board list --plain --columns id,name,type
jira sprint list --plain --columns id,name,state,start,end
```

## Output Flags

- `--plain` — TSV output for clean streaming
- `--raw` — JSON output for structured data
- `--columns key,summary,status` — select specific columns

## Learn more using help

```bash
jira --help
jira issue --help
```

## When to Use

- Searching and viewing Jira issues
- Creating or updating issues, comments, worklogs
- Listing projects, boards, sprints

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/vrslev) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
