---
name: wt-create
description: Create a new git worktree with iTerm integration. Use when asked to create, add, or start a new worktree. Use when this capability is needed.
metadata:
  author: phuongwd
---

Use the `create_worktree` MCP tool to create a new worktree.

Parse `$ARGUMENTS` to extract:
- `ticket` (optional) - Ticket ID like PROJ-123, #42, GH-42
- `name` (required) - Short descriptive name like "user-auth" or "fix-login"
- `mode` (optional) - iTerm mode: `tab`, `split-h`/`h`, or `split-v`/`v`
- `repo` (optional) - Path to source repository

Mode mapping:
- `tab` or empty -> `itermMode: "tab"`
- `split-h` or `h` -> `itermMode: "split-horizontal"`
- `split-v` or `v` -> `itermMode: "split-vertical"`

## Ticket Detection

Automatically detect the issue tracker from the ticket pattern:
- `#123` or `GH-123` -> GitHub
- `ABC-123` pattern -> Linear (default) / Plane / Jira

## Issue Info Fetching

After detecting the tracker, attempt to fetch issue details:

1. **Linear tickets** (e.g., `ABC-123`):
   - Try `mcp__plugin_linear_linear__get_issue` with the ticket ID
   - Extract title and status

2. **Plane tickets**:
   - Try `mcp__plane__retrieve_work_item_by_identifier` with the ticket ID
   - Extract title and status

3. **GitHub issues** (e.g., `#42`, `GH-42`):
   - Use GitHub MCP server tools if available to fetch issue info
   - Or use `gh issue view <number> --json title,state` via Bash
   - Extract title and state

Handle failures gracefully - issue fetching is optional enhancement.

## Examples

- `/wt-create PROJ-123 user-auth` - creates with ticket
- `/wt-create auth-fix` - creates without ticket
- `/wt-create #42 fix-crash` - creates with GitHub issue
- `/wt-create PROJ-123 user-auth split-v` - creates with vertical split
- `/wt-create PROJ-123 auth-fix tab ~/projects/myapp` - creates from specific repo

## Report

After calling the MCP tool, report:
- Worktree path
- Branch name
- Source repository
- Whether dependencies were installed
- iTerm status
- **Issue info** (if fetched): Ticket ID, title, status

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/phuongwd) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
