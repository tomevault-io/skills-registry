---
name: prune-agents
description: | Use when this capability is needed.
metadata:
  author: howells
---

<arc_runtime>
Arc-owned files live under the Arc install root for full-runtime installs.

Set `${ARC_ROOT}` to that root and use `${ARC_ROOT}/...` for Arc bundle files such as
`references/`, `disciplines/`, `agents/`, `templates/`, `scripts/`, and `rules/`.

Project-local files stay relative to the user's repository.
</arc_runtime>

# Prune Orphaned Agents

Run the cleanup script to kill orphaned Claude agent processes.

```bash
${ARC_ROOT}/scripts/cleanup-orphaned-agents.sh
```

This kills Claude Code processes that have become detached from their terminal (TTY shows "??"). These accumulate when the Task tool spawns subagents that don't cleanly exit after completion.

**Safe to run anytime** — only kills orphaned processes. Active terminal sessions are preserved.

After running, report the result to the user including how many processes were cleaned up.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/howells) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
