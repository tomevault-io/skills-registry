---
name: wt-switch
description: Switch to a worktree in iTerm. Use when asked to switch, open, or go to a worktree. Use when this capability is needed.
metadata:
  author: phuongwd
---

Use the `switch_worktree` MCP tool to switch to a worktree.

Parse `$ARGUMENTS` to extract:
- `name` (required) - Worktree name, ticket ID, or partial match
- `mode` (optional) - iTerm mode: `tab`, `split-h`/`h`, or `split-v`/`v`

Mode mapping:
- `tab` or empty → `mode: "tab"`
- `split-h` or `h` → `mode: "split-horizontal"`
- `split-v` or `v` → `mode: "split-vertical"`

The name supports partial matching:
- "auth" matches "PROJ-123-user-auth"
- "PROJ-123" matches any worktree with that ticket

## Examples

- `/wt-switch auth` → switch to worktree matching "auth"
- `/wt-switch PROJ-123` → switch to worktree with ticket PROJ-123
- `/wt-switch auth split-v` → open in vertical split

## Report

Report whether it switched to an existing tab or opened a new one.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/phuongwd) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
