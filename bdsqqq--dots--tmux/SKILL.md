---
name: tmux
description: manage background processes in tmux windows. use when running dev servers, build watchers, or any long-running process that shouldn't block the agent. check window before spawning. Use when this capability is needed.
metadata:
  author: bdsqqq
---
# tmux

manage concurrent processes (servers, builds, watchers) in tmux windows.

## spawn

```bash
tmux new-window -n "name" -d "command"
```

## inspect

```bash
tmux capture-pane -p -t "name"         # visible screen
tmux capture-pane -p -S - -t "name"    # full scrollback
```

## control

```bash
tmux send-keys -t "name" C-c           # interrupt
tmux kill-window -t "name"             # terminate
tmux select-window -t "name"           # switch to
tmux list-windows                      # list all
```

for spawning amp agents with thread linkage, use the `spawn` skill.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/bdsqqq) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
