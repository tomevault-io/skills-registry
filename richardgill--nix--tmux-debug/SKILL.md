---
name: tmux-debug
description: | Use when this capability is needed.
metadata:
  author: richardgill
---

# Tmux Debug

Capture screen content from tmux panes to debug other terminal sessions, monitor background processes, or inspect command output.

## Commands

### List all panes
```bash
tmux list-panes -a -F '#{session_name}:#{window_index}.#{pane_index} #{pane_title}'
```

### Capture current pane
```bash
tmux capture-pane -p
```

```bash
# With ANSI colors
tmux capture-pane -p -e 
```

### Capture specific pane
```bash
tmux capture-pane -t 'session:window.pane' -p
```

### Capture with scrollback history
```bash
# Last 100 lines of scrollback
tmux capture-pane -p -S -100

# Entire scrollback buffer
tmux capture-pane -p -S - -E -
```

## Usage

1. First list panes to find the target: `tmux list-panes -a -F '...'`
2. Capture the pane content: `tmux capture-pane -t 'target' -p`
3. Analyze the output for errors, status, or relevant information

$ARGUMENTS

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/richardgill) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
