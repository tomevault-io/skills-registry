---
name: system-notify
description: Send macOS system notifications from the terminal, including tmux-aware clickable notifications that jump back to a specific pane/window using terminal-notifier. Use when a user asks for desktop/system notifications, task-completion alerts, or "click to jump back to tmux" behavior. Use when this capability is needed.
metadata:
  author: ke-pan
---

# System Notify

## Notify

Use `notify-tmux` for all notifications. It detects tmux automatically:

- Inside tmux: sends a clickable notification that jumps back to the originating pane.
- Outside tmux: sends a standard macOS notification.

```bash
notify-tmux "done"
```

Optional for iTerm2:

```bash
export NOTIFY_TERMINAL_APP="iTerm2"
export NOTIFY_TERMINAL_BUNDLE="com.googlecode.iterm2"
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ke-pan) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
