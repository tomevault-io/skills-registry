---
name: contextualize
description: Read project documentation and get up to speed. Invoke when user wants to understand a codebase, get context, or says "what is this project". Use when this capability is needed.
metadata:
  author: villads-valur
---

# Contextualize

Get up to speed on a project quickly.

## What to Read

### Project Docs (`.claude/`)
- `CLAUDE.md` - Project context and conventions
- `architecture.md` - System design
- `decisions-and-learnings.md` - Technical decisions and gotchas
- `planning.md` - Current work and task tracking

### Worktree Context
- Check `git worktree list` to see if we're in a worktree
- If in a worktree, read its `.claude/planning.md` for current task context
- Note which worktree and what it's for

### Git Status
- `git status` - Current state
- `git log --oneline -10` - Recent work

### GitHub Context
- `gh pr list --author @me` - My open PRs
- `gh pr list --search "review-requested:@me"` - PRs needing my review
- `gh issue list --assignee @me` - Assigned issues

## Output

Concise summary covering:
- What is this project?
- Current status/focus?
- Important decisions or gotchas?
- Recent work?
- Current worktree and task?
- Open PRs or assigned issues?

Focus on what's actionable, not a file dump.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/villads-valur) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
