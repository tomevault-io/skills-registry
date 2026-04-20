---
name: wt-pr
description: Create a GitHub PR from a worktree. Use when asked to create a PR, pull request, or submit changes from a worktree. Use when this capability is needed.
metadata:
  author: phuongwd
---

Use the `create_pr` MCP tool to create a GitHub pull request.

Parse `$ARGUMENTS` to extract:
- `name` (required) - Worktree name, ticket ID, or partial match
- `draft` (optional) - Set to true if "draft", "true", "yes", "1", or "-d" is present

## Examples

- `/wt-pr PROJ-123` - create PR for worktree matching PROJ-123
- `/wt-pr auth draft` - create draft PR for worktree matching "auth"

## Actions

The tool will:
1. Commit any uncommitted changes
2. Push the branch to origin
3. Create the PR via GitHub CLI

## Issue Linking

After creating the PR, link it to the issue tracker:

1. **Detect tracker** from the worktree's ticket:
   - Get worktree info using `list_worktrees`
   - Check the `tracker` field or detect from ticket pattern

2. **Add PR comment to issue**:
   - **Linear**: Use `mcp__plugin_linear_linear__create_comment` with:
     - `issueId`: The ticket ID (e.g., "ABC-123")
     - `body`: "PR opened: #<number> <url>"

   - **Plane**: Use `mcp__plane__create_work_item_comment` with:
     - `identifier`: The ticket ID
     - `comment`: "PR opened: #<number> <url>"

   - **GitHub**: Use GitHub MCP server tools or `gh issue comment` to add PR link
     - Note: GitHub often auto-links PRs to issues when branch contains issue number

3. **Handle failures gracefully**:
   - If MCP tool is unavailable, skip issue linking
   - Report what was done in the final output

## Report

Report the following when complete:
- PR URL
- PR number
- Whether issue was linked (and to which tracker)

**Note:** Requires GitHub CLI (gh) to be installed and authenticated.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/phuongwd) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
