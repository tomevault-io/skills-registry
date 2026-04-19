---
name: dev-processes
description: Run dev servers, tests, long running processes, and watch processes in tmux windows Use when this capability is needed.
metadata:
  author: sethcarlton
---

Run watch processes in tmux windows. Use descriptive names: `dev`, `test`, `build`

If not in tmux (`$TMUX` empty), ensure session exists:

```bash
SESSION="$(basename "$PWD" | tr . _)"
tmux has-session -t "$SESSION" 2>/dev/null || tmux new-session -d -s "$SESSION" -c "$PWD"
```

Check if window exists before creating. If exists, check output instead of restarting:

```bash
tmux select-window -t "$SESSION:dev" 2>/dev/null   # check exists
tmux capture-pane -pt "$SESSION:dev" -S -100       # check output
tmux neww -dn dev -t "$SESSION" 'bun run dev'      # create new
```

## Turborepo

Always use `--ui=stream` (TUI doesn't work in detached sessions):

```bash
turbo run dev --ui=stream
bun run dev -- --ui=stream
```

## SST

Use mono mode to avoid the multiplexer UI in tmux:

```bash
sst dev --mode=mono
bun sst dev --mode=mono
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/sethcarlton) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
