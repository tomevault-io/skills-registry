---
name: ai-switch
description: Use when working with a portable workflow for handing off a coding project between AI agents (Claude Code, Codex CLI, Gemini CLI, Cursor, etc.) without losing context. Uses PROGRESS.md + git as the single source of truth. When the user mentions switching agents, running out of quota, saving progress, or resuming work, route to the appropriate sub-skill — ai-switch-stop (save + commit) or ai-switch-start (read + report). Triggers on "ai-switch", "switch agent", "save progress", "resume from checkpoint", "hand off AI", "out of quota".
metadata:
  author: quyendang
---

# ai-switch — Multi-Agent Project Handoff

This is the umbrella skill for the `ai-switch` workflow. It does not perform actions on its own; it routes to one of two sub-skills.

## What ai-switch solves

Any coding project that is large enough to span multiple AI sessions eventually hits this problem:

- AI agents run out of usage / quota mid-task
- Users want to use different AI tools for different strengths (Claude for architecture, Codex for quick scripts, Gemini for Google ecosystem, etc.)
- Picking up work after a few days means re-explaining everything
- Team members want to hand off work to each other

The `ai-switch` workflow solves this by maintaining two artifacts:

1. **`PROGRESS.md`** at the repo root — a structured file that always describes the current state of the project
2. **`git` history** — the ground truth of what has been committed

Any AI agent that supports SKILL.md can read these two artifacts and seamlessly continue the work.

## Two commands

### `/ai-switch-stop` — End a session

Run this before closing an AI session. The agent will:

1. Summarize what was done
2. Update `PROGRESS.md`
3. Commit all changes (code + progress file)
4. Optionally push to remote

Use it whenever you:

- Hit a usage limit
- Want to switch to another AI
- Are about to stop for the day
- Finish a significant milestone

### `/ai-switch-start` — Begin or resume a session

Run this when starting work on a project with a PROGRESS.md. The agent will:

1. Read PROGRESS.md
2. Cross-verify against git
3. Report the current state back to you
4. Wait for your confirmation before doing any work

Use it whenever you:

- Open a project after a break
- Just switched from another AI agent
- Want a quick status summary

## Routing logic

When invoked as `/ai-switch` or when the user mentions ai-switch generally, ask what they want to do:

```
The `ai-switch` workflow has two commands:

• `/ai-switch-stop` — Save current progress and commit (use when ending a session)
• `/ai-switch-start` — Read progress and resume (use when starting a session)

Which would you like?
```

If the user's intent is obvious from context (e.g. they just said "I ran out of quota"), infer the command and confirm before proceeding.

## Design philosophy

- **Portable**: Works on any SKILL.md-compatible agent. No vendor lock-in.
- **Durable**: Uses git + markdown, both of which outlive any AI tool.
- **Transparent**: PROGRESS.md is human-readable. You can edit it manually.
- **Safe**: Never destructive. Only adds commits, never force-pushes or deletes history.
- **Language-agnostic**: Works for any programming language or project type.

## File layout after setup

```
your-project/
├── .git/
├── PROGRESS.md           ← created/updated by ai-switch
├── src/ ...              ← your code
└── ...
```

## Related skills

- `ai-switch-stop` — implements the stop workflow
- `ai-switch-start` — implements the start/resume workflow

See `references/quick-reference.md` for a printable one-pager of commands and expected outputs.

---
> Source: [quyendang/ai-switch](https://github.com/quyendang/ai-switch) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
