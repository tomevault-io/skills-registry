---
name: reflect
description: Review recent work and suggest new skills, hooks, rules, or agents that could improve future workflows Use when this capability is needed.
metadata:
  author: vivecuervo7
---

# Reflect

Analyze recent activity and the current Claude Code setup to suggest workflow improvements.

## Steps

### Step 1: Gather Context

Read the following in parallel:

- `~/CLAUDE.md` — global instructions
- `~/.claude/settings.json` — permissions, hooks
- `~/.claude/skills/*/SKILL.md` — all global skills
- Recent git history in the current project (if in a git repo): `git log --oneline -20`
- Project-level CLAUDE.md, `.claude/rules/`, `.claude/skills/`, `.claude/agents/` (if in a project)

If the user provided `$ARGUMENTS`, use it to focus your analysis on that area.

### Step 2: Analyze

Look for opportunities in these categories:

| Type | Signal | Example |
|------|--------|---------|
| **Skill** | A multi-step process that's been done manually more than once | "You frequently run build + test + lint in sequence — extract a `/check` skill" |
| **Hook** | A repetitive action that should happen automatically on an event | "You always run tests after editing — add a `PostToolUse` hook on `Edit`" |
| **Rule** | A convention or preference that's been corrected or stated verbally | "You keep reminding Claude about your test framework — add a `.claude/rules/` file" |
| **Agent** | A complex investigation pattern that benefits from isolated context | "Deep architecture analysis keeps filling context — extract an agent" |
| **CLAUDE.md** | A behavioral instruction that applies globally or project-wide | "You always want snake_case — add it to CLAUDE.md" |

Also check for:
- Existing skills/hooks/rules that might be **stale or redundant**
- Global settings that could be **tightened or relaxed**
- Patterns in git history that suggest **missing automation**

### Step 3: Present Suggestions

Present your findings as a concise list, grouped by type. For each suggestion, number it and include:
- What you observed
- What you'd create or change
- Why it would help

### Step 4: Select

After presenting the list, use AskUserQuestion with `multiSelect: true` to let the user pick which suggestions to implement. Each option should be:
- **label**: Number and short label (e.g. `#1 /check skill`, `#3 Auto-lint hook`)
- **description**: One-line summary of what it does

The user can also use "Other" to modify instructions or refine specific items before proceeding.

### Step 5: Implement

For each selected suggestion, implement it. If the user provided custom instructions via "Other", follow those. After implementing, briefly summarize what was created or changed.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/vivecuervo7) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
