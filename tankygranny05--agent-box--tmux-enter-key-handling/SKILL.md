---
name: tmux-enter-key-handling
description: Use when sending input to tmux sessions, especially interactive TUIs like Claude Code or Codex. The Enter keyword fails silently - must use hex codes.
metadata:
  author: tankygranny05
---

# Tmux Enter Key Handling

## What This Solves

When sending input to tmux panes running interactive applications (Claude Code, Codex, vim, etc.), the `Enter` keyword in `tmux send-keys` fails silently. Text appears but is never submitted.

## Working Approach

Send Enter as hex `0x0d` (carriage return) in a separate command:

### Bash
```bash
# ✅ CORRECT - send text, then Enter as hex
tmux send-keys -t "session-name" "Hello world"
tmux send-keys -t "session-name" -H 0d
```

### Python
```python
import subprocess

# Send text
subprocess.run(['tmux', 'send-keys', '-t', session, prompt])

# Send Enter as hex (REQUIRED for TUIs)
subprocess.run(['tmux', 'send-keys', '-t', session, '-H', '0d'])
```

### Other Special Keys

| Key    | Hex Code | Usage                          |
|--------|----------|--------------------------------|
| Enter  | `0d`     | `tmux send-keys -t sess -H 0d` |
| Escape | `1b`     | `tmux send-keys -t sess -H 1b` |
| Tab    | `09`     | `tmux send-keys -t sess -H 09` |

## Failed Attempts

⚠️ **Read this first** - These approaches don't work:

| Attempt | Why It Failed | Time Wasted |
|---------|---------------|-------------|
| `tmux send-keys -t sess "text" Enter` | Enter keyword ignored by TUI apps | 2+ hours debugging |
| `subprocess.run([..., prompt, 'Enter'])` | Same issue - Python passes it but TUI ignores | 1 hour |
| Checking return code for success | returncode=0 means tmux ran, NOT that input was submitted | 30 min false confidence |

## Verification (Critical!)

**Never trust return codes.** Always verify visually:

```bash
# Send prompt
tmux send-keys -t sess "Write a haiku"
tmux send-keys -t sess -H 0d

# Wait for response
sleep 5

# VERIFY the response actually appeared
tmux capture-pane -t sess -p -S -50
```

Only report success when you SEE:
1. The prompt text in output
2. Processing indicator (e.g., "Thinking...")
3. **Actual response content**
4. New input prompt ready

## Environment & Dependencies

- **Applies to**: Any tmux version
- **Affected apps**: Claude Code, Codex CLI, vim, emacs, any ncurses/TUI app
- **Works fine with**: Regular shell prompts (bash, zsh) - but hex is safer everywhere

## Session Reference

- **Date**: 2024
- **Source**: tmux_sdk development, multiple debugging sessions
- **Original issue**: Commands appeared typed but never executed

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/tankygranny05) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
