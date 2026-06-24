---
name: nvim
description: | Use when this capability is needed.
metadata:
  author: richardgill
---

# Open Files in Nvim

Open files in the running nvim instance connected to this tmux session.

## Command

```bash
nvim --server "$NVIM_SOCKET" --remote <files...>
```

## Environment

- `$NVIM_SOCKET` - Path to the nvim socket (set automatically in worktree sessions)

## Examples

```bash
# Open a single file
nvim --server "$NVIM_SOCKET" --remote src/index.ts

# Open multiple files
nvim --server "$NVIM_SOCKET" --remote src/index.ts src/utils.ts

# Open file at specific line (use +line before filename)
nvim --server "$NVIM_SOCKET" --remote +42 src/index.ts
```

## Troubleshooting

- **Socket not set**: Ensure you're in a worktree session, or manually set `NVIM_SOCKET`
- **Connection refused**: The nvim instance may have closed; restart nvim with `v . --listen $NVIM_SOCKET`
- **File not opening**: Verify the file path is correct and accessible

## Notes

- The socket is set via `tmux set-environment` at session creation
- New panes/windows in the session inherit `NVIM_SOCKET`

$ARGUMENTS

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/richardgill) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
