---
name: github-pr-manager
description: Manage GitHub Pull Requests, Issues, and Reviews. Use this for "create PR", "check PR status", or "list issues". Use when this capability is needed.
metadata:
  author: spagestic
---

# GitHub Manager Skill

Uses the official GitHub MCP server to interact with the repository.

## Usage

**Command Template:**

```bash
npx mcporter call --command "npx -y @modelcontextprotocol/server-github" --tool <tool_name> --args <json_args>
```

## Common Tools

- `create_pull_request`: Create a new PR from the current branch.

- `list_pull_requests`: See active PRs.

- `add_issue_comment`: Comment on an issue or PR.

Example: List open PRs

```bash
npx mcporter call --command "npx -y @modelcontextprotocol/server-github" --tool list_pull_requests --args '{"state": "open", "repo": "owner/repo"}'
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/spagestic) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
