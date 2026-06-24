---
name: ai-switch-stop
description: Save the current project progress into PROGRESS.md and commit all changes so that a different AI agent can continue the work in a later session. Use this whenever the user runs out of usage/quota, wants to switch AI agents (Claude Code ↔ Codex ↔ Gemini CLI ↔ Cursor), or stops work to resume later. Trigger on phrases like "ai-switch stop", "/ai-switch-stop", "save progress and commit", "out of quota switch agent", "hand off to another AI". Use when this capability is needed.
metadata:
  author: quyendang
---

# /ai-switch-stop — Save Progress and Hand Off

This skill captures the current state of work into `PROGRESS.md` and commits everything so the next AI agent (or future you) can resume seamlessly.

## When to use this skill

Invoke when the user:

- Runs `/ai-switch-stop` or `/ai-switch stop` or says "ai-switch stop"
- Mentions running out of quota / usage limit
- Wants to switch to a different AI coding agent
- Wants to stop work and commit a checkpoint
- Says "save progress", "checkpoint", "hand off"

## Required workflow — follow in order

### Step 1 — Verify environment

Before doing anything, check:

```bash
# Are we in a git repo?
git rev-parse --is-inside-work-tree

# What's the current branch?
git branch --show-current

# Any uncommitted changes?
git status --short
```

If not in a git repo, STOP and tell the user: "This skill requires a git repository. Please run `git init` first or navigate to a git project."

### Step 2 — Locate or create PROGRESS.md

Check if `PROGRESS.md` exists at the repo root:

```bash
test -f PROGRESS.md && echo "exists" || echo "missing"
```

- **If it exists**: Read it entirely to understand current state
- **If it does not exist**: Create it using the template at `references/progress-template.md` (also shown below). Fill in what you know from the conversation so far.

### Step 3 — Summarize what was done in this session

Before writing anything, reflect on the conversation:

1. What was the user's goal for this session?
2. What files were created or modified? Check git diff:
   ```bash
   git diff --stat
   git diff --name-only --cached
   git ls-files --others --exclude-standard
   ```
3. What is completed vs still in progress?
4. Are there any blockers, half-finished things, or decisions that need to be documented?
5. What is the most logical next step?

Keep this summary factual — do not guess. If unsure, mark items as "(verify with user)".

### Step 4 — Update PROGRESS.md

Update the following sections. DO NOT delete existing history; only APPEND to changelog and update current-state sections.

**Sections to update:**

1. **Header** — update "Last updated" timestamp and "Last agent"
2. **Current phase / task** — reflect what the user is working on right now
3. **Status** — change status emoji if the work state changed (🚧 in progress, ✅ complete, ⏸ paused, ⚠️ has issues)
4. **Todo list** — mark completed items with `[x]`, leave in-progress items with `[~]`, keep pending items as `[ ]`
5. **Files changed this session** — reset and fill with files from `git diff --name-only` of current session
6. **Decisions & notes** — APPEND any design/technical decisions made during this session
7. **Blockers & issues** — update: remove resolved, add new
8. **Next up** — 2-3 concrete next actions for whoever resumes
9. **Changelog** — APPEND a new row with today's date, agent name, and 1-line summary

**Writing style:**

- Be concrete and specific. "Implemented login form validation for empty fields" not "worked on login".
- Use the same language the user is using (Vietnamese, English, etc.)
- Reference exact file paths, function names, and line numbers where useful
- If a task was blocked, describe what specifically is blocking it

### Step 5 — Verify PROGRESS.md is valid

After writing, re-read the file and confirm:

- Tables still render as valid markdown
- No section was accidentally deleted
- Dates are in consistent format
- "Next up" is actionable (someone could pick up and work immediately)

### Step 6 — Commit and push

Stage and commit all changes, including the updated PROGRESS.md:

```bash
# Stage everything (code + PROGRESS.md)
git add -A

# Show what will be committed for user visibility
git status --short

# Commit with a descriptive message
git commit -m "checkpoint: [brief description of what was done]

Session summary:
- [key accomplishment 1]
- [key accomplishment 2]

Next up: [what the next agent should do]

Agent: [agent-name, e.g. Claude Code / Codex / Gemini CLI]"
```

**If there is a remote configured**, offer to push:

```bash
# Check if remote exists
git remote -v

# If yes, ask user: "Should I push to remote? (recommended for cross-device handoff)"
# On confirm:
git push
```

If push fails (no remote, auth issue, conflict), report the error but do not fail the whole skill — the local commit is still valuable.

### Step 7 — Final report to user

Tell the user:

```
✅ Progress saved successfully.

📝 PROGRESS.md updated with:
  - [what changed in the file]

💾 Committed: [commit SHA short]
🌿 Branch: [branch name]
🚀 Pushed: [yes/no]

🎯 Next agent should:
  1. [next action 1]
  2. [next action 2]

To resume in another AI agent, run: /ai-switch-start
```

## Important rules

- **Never lose data.** If PROGRESS.md existed, preserve its history. Append, do not overwrite.
- **Never skip git commit.** The entire value of this skill is the checkpoint. Without a commit, the next agent cannot verify state.
- **Do not include secrets.** Never write API keys, tokens, credentials, or passwords into PROGRESS.md. If you encounter any, redact them as `[REDACTED]` and warn the user.
- **Be honest about uncertainty.** If you are not sure whether something is complete, mark it as `[~]` in progress and note it needs verification.
- **Respect existing commit hooks.** If the repo has pre-commit hooks and they fail, report the failure to the user rather than bypassing with `--no-verify`.
- **Do not create new branches.** Commit to whatever branch the user is on.

## Edge cases

**Already clean working tree (nothing to commit):**
Still update PROGRESS.md if there is session context worth saving (e.g. research, planning discussion). Commit the PROGRESS.md change alone with message: `docs: update progress checkpoint`.

**Merge conflicts in PROGRESS.md:**
Prefer preserving both versions of history. Merge by combining changelog entries in chronological order. If conflict is complex, show both versions to the user and ask how to resolve.

**Very long session with many changes:**
Summarize high-level rather than listing every file change. Keep the PROGRESS.md "Files changed" section under 30 items. Group related files together.

**No PROGRESS.md template available:**
Fall back to the inline template in `references/progress-template.md`. If that is also missing, use the minimal version in the "Minimal PROGRESS.md template" section below.

## Minimal PROGRESS.md template

If `references/progress-template.md` is not accessible, use this:

```markdown
# Project Progress

**Last updated:** [YYYY-MM-DD HH:MM]
**Last agent:** [agent name]
**Status:** 🚧 In progress

## Current task

[what is being worked on]

## Todo list

- [x] Completed item
- [~] In progress item
- [ ] Pending item

## Files changed this session

- `path/to/file.ext` — [what changed]

## Decisions & notes

- [date] [decision or note]

## Blockers

- None / [description]

## Next up

1. [concrete next action]
2. [concrete next action]

## Changelog

| Date | Agent | Summary |
|---|---|---|
| YYYY-MM-DD | [agent] | [one-line summary] |
```

## Cross-agent notes

This skill must work identically across Claude Code, Codex CLI, Gemini CLI, Cursor, and any other agent supporting the SKILL.md standard. Do not rely on agent-specific APIs. Use only:

- Standard POSIX shell commands (bash, zsh compatible)
- `git` CLI
- Standard file reading/writing

Do not use:

- Claude Code-only tools
- Codex-only MCP servers
- Agent-specific slash commands other than this one

If the agent asks for permission to run `git` or file operations, grant it — this skill is safe by design (no destructive operations beyond commits).

---
> Source: [quyendang/ai-switch](https://github.com/quyendang/ai-switch) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
