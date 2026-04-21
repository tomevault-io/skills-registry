---
name: git-commit-workflow
description: Git commit workflow with conventional commits and staging hygiene. Use when asked to stage changes, write commit messages, or manage commits in multi-worktree setups. Use when this capability is needed.
metadata:
  author: 0xabrar
---

# Git Commit Workflow

## Overview

Standardize staging and conventional commits. Favor minimal, clear steps and ask for missing details (scope, type) only when needed.

## Conventional commits

- Format: `type(scope): short description` (lowercase, imperative)
- Group logical changes into separate commits
- Stage related files together

Common types: `feat`, `fix`, `refactor`, `chore`, `docs`, `test`, `build`, `ci`, `perf`, `style`

Multi-line commit (heredoc):
```
git commit -m "$(cat <<'EOF'
feat(ui): add new button component

- Create reusable Button component with multiple variants
- Add TypeScript support and proper prop types
- Include Storybook stories for documentation
EOF
)"
```

## Multi-worktree notes

If working across multiple worktrees (e.g. `ansel-agent-a`, `ansel-agent-b`):
- Confirm the active worktree before running commands:
  - `pwd`
  - `git status -sb`
  - `git rev-parse --show-toplevel`
- List worktrees when unsure:
  - `git worktree list`
- Use `git -C /path/to/worktree ...` if needed

## Troubleshooting

- No commits when expected:
  - `git status -sb`
  - `git log --oneline -n 5`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/0xabrar) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
