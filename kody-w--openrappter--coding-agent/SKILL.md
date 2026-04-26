---
name: coding-agent
description: Run Codex CLI, Claude Code, OpenCode, or Pi Coding Agent for programmatic code generation and review. Use when this capability is needed.
metadata:
  author: kody-w
---

# Coding Agent

Use coding agents (Codex, Claude Code, OpenCode, Pi) for automated development tasks.

## Quick Start

```bash
# Codex (needs git repo)
codex exec "Add error handling to API calls"

# Claude Code
claude "Refactor the auth module"

# OpenCode
opencode run "Write unit tests"

# Pi
pi "Add dark mode support"
```

## Background Mode

For longer tasks, run in background and monitor:

```bash
# Start in background
codex exec --full-auto "Build a REST API" &

# Monitor output
tail -f /tmp/codex-output.log
```

## Best Practices

- Always run in a git repository
- Use `--full-auto` for autonomous work
- Monitor progress for long-running tasks
- Review changes before committing

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/kody-w) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
