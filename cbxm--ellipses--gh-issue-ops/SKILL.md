---
name: gh-issue-ops
description: Provides scripts for issue CRUD operations. All scripts auto-detect the current repo.
metadata:
  author: cbxm
---
---
name: gh-issue-ops
description: GitHub issue operations including fetching full issue details, setting issue type, and getting timeline events. Use when creating, reading, or modifying GitHub issues.
allowed-tools: Bash, Read
---

# GitHub Issue Operations

Provides scripts for issue CRUD operations. All scripts auto-detect the current repo.

## Available Scripts

### get-issue.sh <number>
Returns full issue details including type (via GraphQL).
```bash
~/.claude/skills/gh-issue-ops/scripts/get-issue.sh 4
```

### set-issue-type.sh <number> <type-name>
Sets issue type by name (resolves ID dynamically).
```bash
~/.claude/skills/gh-issue-ops/scripts/set-issue-type.sh 4 "Bug"
```

### get-issue-timeline.sh <number>
Returns timeline events for an issue (status changes, comments, etc).
```bash
~/.claude/skills/gh-issue-ops/scripts/get-issue-timeline.sh 4
```

### get-issue-comments.sh <number>
Returns all comments on an issue.
```bash
~/.claude/skills/gh-issue-ops/scripts/get-issue-comments.sh 4
```

## Usage

Run scripts with issue number as argument:
```bash
bash ~/.claude/skills/gh-issue-ops/scripts/get-issue.sh 4
```

Type names are case-insensitive and matched against available types in the repo.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/cbxm) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
