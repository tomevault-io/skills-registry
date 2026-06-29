---
name: worktree-setup
description: ALWAYS use this skill when creating a git worktree. Sets up symlinks to shared gitignored files. Use when this capability is needed.
metadata:
  author: PinW
---

Create a worktree and set up symlinks:

```bash
python3 .claude/skills/worktree-setup/scripts/setup-worktree.py <project-name> <branch-name>
```

---
> Source: [PinW/whisper-key-local](https://github.com/PinW/whisper-key-local) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-29 -->
