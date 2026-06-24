---
name: workspace-status
description: Show status of all projects in the workspace including git branches, uncommitted changes, and running services. Use when user asks "workspace status", "show all projects", or "what's the status". Use when this capability is needed.
metadata:
  author: patricio0312rev
---

# Skill: Workspace Status

## Description

Show the status of all projects in the current workspace, including git status, running services, and last commits.

## Instructions

When the user wants to see the workspace status:

### Step 1: Detect Current Workspace

First, detect which workspace the user is in by checking:
- Current directory against project paths in `~/.claude/workspaces/*/WORKSPACE.md`
- Git remote URL against configured repos

If no workspace is detected, prompt the user to either:
- Run `/workspaces:init` to create a new workspace
- Navigate to a project directory that's part of a workspace

### Step 2: Display Status

Show a formatted table with information about all projects:

```
📁 Workspace: Acme Corp

┌─────────────┬──────────────┬─────────────┬──────────┬─────────┐
│ Project     │ Branch       │ Git Status  │ Service  │ Port    │
├─────────────┼──────────────┼─────────────┼──────────┼─────────┤
│ api         │ main         │ clean       │ running  │ 3000    │
│ admin       │ feature/auth │ dirty (+3)  │ stopped  │ 3001    │
│ homepage    │ main         │ clean, ↑2   │ running  │ 3002    │
│ mobile      │ main         │ not cloned  │ -        │ 8081    │
└─────────────┴──────────────┴─────────────┴──────────┴─────────┘
```

### Status Values

**Git Status:**
- `clean` - No uncommitted changes
- `dirty (+N)` - N files changed
- `↑N` - N commits ahead of remote
- `↓N` - N commits behind remote
- `not cloned` - Repository hasn't been cloned yet

**Service Status:**
- `running` - Port is in use
- `stopped` - Port is not in use

### Additional Information

Show recent commits and any warnings:

```
Recent commits:
  • api: "fix: resolve auth token issue" (2 hours ago)
  • admin: "feat: add user dashboard" (1 day ago)

⚠ Warning: 'admin' has uncommitted changes
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/patricio0312rev) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
