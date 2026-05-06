---
name: ralph-loop-soulbrews
description: Self-referential AI loops with session isolation. Soul Brews edition of Ralph Wiggum technique. Use when this capability is needed.
metadata:
  author: neversight
---

# Ralph Loop - Soul Brews Edition

> "Iteration > Perfection"

Self-referential development loops with **session isolation**.

## Source Attribution

```
Base:     anthropics/claude-plugins-official/plugins/ralph-loop
Extended: Soul-Brews-Studio/ralph-local (PR #15853)
```

## Usage

```bash
/ralph-loop "Build a REST API" --completion-promise "DONE" --max-iterations 50
/cancel-ralph       # Cancel active loop
/help               # Show documentation
```

## How It Works

1. `/ralph-loop` creates state file with session ID
2. Work on the task
3. When you try to exit, Stop hook intercepts
4. Same prompt fed back (see previous work in files)
5. Loop until `<promise>DONE</promise>` or max iterations

## Session Isolation

Each session has its own state file:

```
state/${SESSION_ID}.md
```

No interference between terminals!

## Commands

Read the command files in `commands/` directory:
- `ralph-loop.md` - Start loop
- `cancel-ralph.md` - Cancel loop
- `help.md` - Documentation

## Note: Hooks Required

This skill requires Claude Code's hook system (Stop hook, SessionStart hook).
It will be installed to `~/.claude/plugins/ralph-loop-soulbrews/`.

ARGUMENTS: $ARGUMENTS

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/neversight) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
