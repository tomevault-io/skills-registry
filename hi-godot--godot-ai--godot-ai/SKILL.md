---
name: godot-ai
description: Build, test, and extend the Godot AI server and editor plugin Use when this capability is needed.
metadata:
  author: hi-godot
---

# Godot AI Development

This Claude skill is an adapter. The vendor-neutral source of truth is
`AGENTS.md` at the repository root.

Read `AGENTS.md` before making changes. It covers project structure, tool
surface rules, test expectations, GDScript and Python conventions, worktree
safety, and release compatibility.

Claude-specific reminders:

- Claude Code may run in `.claude/worktrees/<name>`. Confirm the worktree and
  connected Godot editor session before editing plugin code or running
  Godot-side tests.
- Keep general repo guidance in `AGENTS.md`; only Claude-specific loading or
  workflow notes belong in this skill file.

---
> Source: [hi-godot/godot-ai](https://github.com/hi-godot/godot-ai) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-30 -->
