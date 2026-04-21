---
name: retrospective
description: > Use when this capability is needed.
metadata:
  author: t2hnd
---

# Retrospective Skill

Analyze the current session to extract reusable patterns, recurring workflows, and skill candidates.

## Workflow

### 1. Session Summary

Review the conversation history and summarize:
- **What was done:** features implemented, bugs fixed, tasks completed
- **What tools/commands were used repeatedly**
- **What friction occurred:** errors, retries, misunderstandings, manual steps

### 2. Pattern Detection

Look for these signals that indicate a workflow should become a skill:

**Repetition signals:**
- Same sequence of commands executed 2+ times in this session
- A workflow that was also done in previous sessions (check MEMORY.md)
- Manual steps that could be automated (e.g., "start server, then check proxy, then curl endpoint")

**Friction signals:**
- Steps where Claude had to retry or the user had to correct
- Configuration that was forgotten and had to be redone
- Verification steps that were skipped and caused issues later

**Complexity signals:**
- Multi-step workflows with a specific order (build → test → deploy)
- Domain-specific knowledge needed (project conventions, API patterns)
- Checklists that need to be followed consistently

### 3. Evaluate Candidates

For each candidate pattern, assess:

| Criteria | Question |
|----------|----------|
| **Frequency** | Will this be used again? (weekly+) |
| **Complexity** | Is it more than 2 trivial steps? |
| **Error-prone** | Are steps likely to be forgotten or done wrong? |
| **Generic** | Does it apply across projects or just this one? |

**Score each candidate:**
- 3+ criteria met → Strong skill candidate
- 2 criteria met → Consider as skill
- 1 or fewer → Not worth a skill (just document in MEMORY.md)

### 4. Report

Present findings in this format:

```markdown
## Session Retrospective

### What was done
- [summary of work]

### Skill candidates found

#### 1. [Pattern name] ⭐ Strong candidate
- **What:** [description of the workflow]
- **Why:** [which signals triggered: repetition/friction/complexity]
- **Frequency:** [how often this would be used]
- **Example from this session:** [concrete example]

#### 2. [Pattern name] 🔶 Consider
- **What:** [description]
- **Why:** [signals]
- ...

### Project-level learnings (for project MEMORY.md)
- [project-specific conventions discovered]
- [project-specific gotchas encountered]
- [patterns tied to this codebase's architecture or domain]

### General learnings (for ~/.claude/MEMORY.md)
- [language/tool gotchas applicable across projects]
- [reusable techniques or best practices discovered]
- [insights about tools, libraries, or APIs]
```

### 5. Create Skills (with permission)

For strong candidates, ask the user:
```
Found N skill candidates. Create them?
1. ⭐ [name] — [one-line description]
2. ⭐ [name] — [one-line description]
3. 🔶 [name] — [one-line description]
```

If approved, create each skill at `~/.claude/skills/<name>/SKILL.md` with:
- YAML frontmatter (`name`, `description` with triggers)
- Clear workflow steps
- Important rules section

### 6. Update Memory

Classify each learning and write to the appropriate MEMORY.md:

**Project-level → `<project-root>/MEMORY.md`**
- Conventions specific to this codebase (naming, ordering, architecture)
- Domain-specific gotchas (e.g., pattern priority rules for this PII sanitizer)
- Project-specific configuration or workflow notes

**General-level → `~/.claude/MEMORY.md`**
- Language or tool gotchas applicable across any project (e.g., JS regex alternation)
- Reusable techniques or best practices
- Insights about libraries, APIs, or tools

Ask the user before writing if the classification is ambiguous.

## Skill Creation Checklist

When creating a new skill from a detected pattern:

- [ ] File: `~/.claude/skills/<name>/SKILL.md` (uppercase SKILL.md)
- [ ] YAML frontmatter with `name`, `description`, and Japanese + English triggers
- [ ] Clear step-by-step workflow
- [ ] "Important Rules" section with do's and don'ts
- [ ] Concise — under 150 lines if possible

## Important Rules

- **DO review the full session**, not just the last few messages
- **DO check existing skills** (`~/.claude/skills/`) to avoid duplicates
- **DO check MEMORY.md** for patterns that appeared in previous sessions too
- **DO NOT create skills for one-off tasks** — only recurring patterns
- **DO NOT create skills without user approval**
- Present candidates clearly so the user can decide which to create

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/t2hnd) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
