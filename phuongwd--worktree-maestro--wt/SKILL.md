---
name: wt
description: List all git worktrees with status. Use when asked to show, list, or view worktrees. Use when this capability is needed.
metadata:
  author: phuongwd
---

Use the `list_worktrees` MCP tool with `verbose: true` and `allRepos: true` to show all worktrees.

If `$ARGUMENTS` contains a path, pass it as `repo` to filter worktrees from that specific repository.

## Issue Tracker Integration

After listing worktrees, enhance the display with issue information:

1. For each worktree with a ticket:
   - **Linear tickets** (e.g., `ABC-123`): Try `mcp__plugin_linear_linear__get_issue` to fetch title/status
   - **Plane tickets**: Try `mcp__plane__retrieve_work_item_by_identifier` to fetch title/status
   - **GitHub issues** (e.g., `#42`, `GH-42`): Use GitHub MCP server tools if available, or `gh issue view <number> --json title,state` via Bash

2. Handle unavailability gracefully:
   - If MCP tool fails or is unavailable, show ticket ID only
   - If tracker cannot be determined, show ticket ID as-is

## Display Format

Show results in a clear table with columns:
- Repository (if multiple repos)
- Name
- Branch
- Status (clean/dirty)
- Changed files count
- **Issue** (ticket ID + title + status when available)
- Port (if assigned)
- iTerm tab status

Example output:
```
| Name                | Branch               | Status | Changes | Issue                           |
|---------------------|----------------------|--------|---------|----------------------------------|
| myapp-ABC-123-auth  | feature/ABC-123-auth | clean  | -       | ABC-123: OAuth flow [In Progress]|
| myapp-hotfix        | feature/hotfix       | dirty  | 3 files | -                                |
| myapp-GH-42-fix     | feature/GH-42-fix    | clean  | -       | #42: Fix crash [open]            |
```

When tracker unavailable:
```
| myapp-ABC-123-auth  | feature/ABC-123-auth | clean  | -       | ABC-123                          |
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/phuongwd) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
