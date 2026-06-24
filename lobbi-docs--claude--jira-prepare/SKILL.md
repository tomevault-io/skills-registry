---
name: jiraprepare
description: Prepare a Jira task with enrichment and subtask creation. Use when the user wants to "prepare task", "enrich issue", "create subtasks", or "prepare for development". Use when this capability is needed.
metadata:
  author: lobbi-docs
---

# Jira Task Preparation

Prepare a Jira task with technical enrichment, subtask creation, and work breakdown structure.

## Usage

```
/jira:prepare <issue-key>
```

## Features

- Analyzes issue requirements
- Creates technical subtasks
- Enriches description with implementation notes
- Identifies dependencies
- Estimates effort

## Related Commands

- `/jira:work` - Start working on the prepared issue
- `/jira:triage` - Triage and analyze issues

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/lobbi-docs) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
