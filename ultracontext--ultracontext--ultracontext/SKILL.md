---
name: switch
description: Switch current session to codex. UltraContext keeps your original context. Use when this capability is needed.
metadata:
  author: ultracontext
---

# /switch — Cross-agent session portability by UltraContext

Switch your current conversation to another AI agent with full context.

## Usage

`/switch <target>` where target is: `codex` or `claude`

Optional flags: `--last N` (carry only last N messages), `--no-launch` (write session file only)

## Steps

1. Run:
```bash
ultracontext switch $ARGUMENTS
```

2. If `ultracontext` is not installed, tell the user: `npm i -g ultracontext` or `bun add -g ultracontext`

3. Report: session ID, file path, message count. Codex will open in a new terminal tab automatically.

---
> Source: [ultracontext/ultracontext](https://github.com/ultracontext/ultracontext) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-29 -->
