---
name: ai-switch-start
description: Resume a project previously checkpointed with ai-switch-stop. Reads PROGRESS.md and inspects git history to understand exactly where the last agent left off, then reports the current state and asks the user before continuing. Use whenever the user starts a new session on a project that has a PROGRESS.md, switches from another AI agent, or says phrases like "ai-switch start", "/ai-switch-start", "continue where we left off", "resume project", "I just switched from Claude/Codex/Gemini". Use when this capability is needed.
metadata:
  author: quyendang
---

# /ai-switch-start — Resume Work From Checkpoint

This skill restores context from a previous session. It reads `PROGRESS.md` and cross-verifies against git history, then reports state to the user and waits for approval before doing any work.

## When to use this skill

Invoke when the user:

- Runs `/ai-switch-start` or `/ai-switch start` or says "ai-switch start"
- Starts a new session on a project with an existing `PROGRESS.md`
- Just switched from another AI agent (Claude Code, Codex, Gemini CLI, Cursor)
- Says "resume", "continue where we left off", "pick up from last session"
- Mentions opening a project after a break

## Core principle — DO NOT code until approved

This skill is read-only until the user explicitly confirms the next action. The goal is to restore shared understanding, not to jump in and make changes.

## Required workflow — follow in order

### Step 1 — Verify environment

```bash
# Confirm git repo
git rev-parse --is-inside-work-tree

# Get branch and last commit info
git branch --show-current
git log --oneline -10
git status --short
```

If not a git repo: "This skill expects a git-tracked project. If you are starting fresh, use `/ai-switch-stop` after your first session to initialize tracking."

### Step 2 — Read PROGRESS.md

```bash
test -f PROGRESS.md && cat PROGRESS.md || echo "MISSING"
```

**If PROGRESS.md is missing:**

Tell the user:

```
⚠️ No PROGRESS.md found at repo root.

This project does not have a checkpoint file yet. If you just want to start fresh, go ahead with your request.

If you expected a checkpoint to exist:
- Check if you are in the correct directory (pwd)
- Check if it is on a different branch (git branch -a)
- Check git log for a recent checkpoint commit (git log --grep=checkpoint)
```

Then STOP. Wait for user to direct.

**If PROGRESS.md exists:** Read it entirely. Extract:

- Current phase / task
- Status (🚧 🟢 ⏸ ⚠️)
- Last updated timestamp
- Last agent name
- Todo list (marked / unmarked items)
- Files changed in last session
- Recent decisions
- Active blockers
- Next up list
- Last few changelog rows

### Step 3 — Cross-verify with git

Check if git state matches what PROGRESS.md claims:

```bash
# Last commit date and message
git log -1 --format="%cd | %s" --date=format:"%Y-%m-%d %H:%M"

# Files touched in last commit
git show --stat HEAD

# Uncommitted changes (should usually be none if last session used ai-switch-stop)
git status --short

# Any un-pushed commits?
git log @{u}.. 2>/dev/null || echo "No upstream or all pushed"
```

Look for mismatches:

- PROGRESS.md says "✅ complete" but there are uncommitted changes → something was not checkpointed
- PROGRESS.md lists files in "Files changed" but they are not in git → state drift
- Last commit is much older than PROGRESS.md "Last updated" → file edited without commit
- Last commit is much newer than PROGRESS.md → someone committed without updating progress

Flag these mismatches in the report.

### Step 4 — Check environment signals

Quick sanity checks (skip if not applicable to the project):

```bash
# Node projects
test -f package.json && test -d node_modules && echo "deps installed" || echo "deps missing"

# Python
test -f requirements.txt -o -f pyproject.toml && echo "python project"

# Build status (if there is a recent build output)
ls -la dist/ build/ .next/ 2>/dev/null | head -5
```

Do not install anything automatically. Just note state for the report.

### Step 5 — Produce the resume report

Give the user a structured status report. Use the template below, adapted to the user's language:

```
📍 Resume Report

**Project:** [name from README or folder]
**Last checkpoint:** [timestamp from PROGRESS.md]
**Last agent:** [name]
**Current branch:** [branch name]
**Last commit:** [SHA short | message | date]

**📊 Status:** [🚧 / ✅ / ⏸ / ⚠️ + label]

**📝 Current task:**
[summary from PROGRESS.md]

**✅ Recently completed:**
- [item 1 from todos marked [x]]
- [item 2]
[keep to 3-5 most recent]

**🔄 In progress:**
- [items marked [~]]

**⏳ Pending:**
- [top 3 items marked [ ]]

**📂 Files touched in last session:**
- [from "Files changed" section, top 5]

**💡 Key decisions:**
- [last 3 notes from "Decisions & notes"]

**⚠️ Active blockers:**
- [from "Blockers" section, or "None"]

**🔍 State check:**
- Git working tree: [clean / has uncommitted changes]
- Mismatches detected: [none / list]
- Dependencies: [installed / need install]

**🎯 Suggested next action:**
[first item from "Next up" in PROGRESS.md, as a concrete action]

---

**❓ Before I continue, please confirm:**

1. Is the above state accurate? Anything changed outside my knowledge?
2. Should I proceed with the suggested next action, or do you want to go a different direction?
3. Any new context I should know about (design change, new requirement, bug report)?
```

### Step 6 — Wait for user confirmation

Do not begin work. The user will either:

- Confirm to proceed with suggested action → then you can work normally
- Redirect to a different task → follow the new direction
- Flag corrections to the state → update PROGRESS.md to reflect reality first
- Ask questions → answer them

### Step 7 — When resuming work

Once approved, carry on as a normal session. Remember to:

1. Follow the conventions described in PROGRESS.md "Decisions & notes"
2. Respect blockers listed
3. At the end of the session, run `/ai-switch-stop` to save your own checkpoint

## Important rules

- **Never trust memory over PROGRESS.md.** If your training data or prior context suggests something different from what PROGRESS.md says, believe the file. It is the source of truth.
- **Never trust PROGRESS.md over git.** If git state clearly contradicts the file (e.g. files listed as created but do not exist on disk), flag the mismatch.
- **Do not auto-execute the next step.** Always wait for user confirmation. Agents from different providers have different default behaviors; being conservative here is universal.
- **Do not read secrets.** If PROGRESS.md contains anything that looks like an API key or credential (it should not), warn the user and suggest redaction.
- **Respect language.** Mirror the language used in PROGRESS.md when delivering the report.

## Edge cases

**PROGRESS.md exists but is empty or malformed:**
Tell the user: "PROGRESS.md exists but I could not parse it clearly. Here is what I can read from git alone:" and produce a git-only summary. Suggest running `/ai-switch-stop` at the end of the session to rebuild a clean checkpoint.

**Multiple branches with different PROGRESS.md:**
Only read the current branch's file. Mention in the report: "Note: This is the PROGRESS.md on branch `{branch}`. Other branches may have different state."

**Detached HEAD:**
Warn the user and suggest checking out a branch before working.

**Merge or rebase in progress:**
Check `.git/MERGE_HEAD` or `.git/rebase-merge/`. If detected, tell user: "Repo is mid-merge/rebase. Please finish it before resuming." Do not proceed with work.

**Very old checkpoint (> 30 days):**
Still produce the report but add a note: "This checkpoint is old. Consider reviewing whether decisions and plans are still relevant."

**First run on a fresh clone from remote:**
If `git log` shows the last commit was an `ai-switch-stop` checkpoint from another machine, treat it as a successful resume. Check if dependencies need installing (note in state check but do not auto-install).

## Cross-agent notes

This skill uses only:

- Standard POSIX shell (bash, zsh compatible)
- `git` CLI
- Standard file reading

It does not depend on any agent-specific tooling. It works on Claude Code, Codex CLI, Gemini CLI, Cursor, Windsurf, Continue.dev, Aider, and any other agent that supports the SKILL.md standard.

If the user switches between agents mid-project, each agent reads the same PROGRESS.md and produces the same report. The handoff is symmetric.

---
> Source: [quyendang/ai-switch](https://github.com/quyendang/ai-switch) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
