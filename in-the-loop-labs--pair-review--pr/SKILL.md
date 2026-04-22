---
name: pr
description: > Use when this capability is needed.
metadata:
  author: in-the-loop-labs
---

# PR Review

Open the current branch's GitHub PR in the pair-review web UI.

## Steps

1. Call the `mcp__pair-review__get_server_info` tool to get the server URL.
2. Determine the GitHub owner, repo, and PR number for the current branch.
3. Open the browser: `open "{url}/pr/{owner}/{repo}/{number}"`

## Error handling

- If `get_server_info` fails, tell the user to start pair-review first: `npx @in-the-loop-labs/pair-review --mcp`
- If no PR exists for the current branch, tell the user.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/in-the-loop-labs) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
