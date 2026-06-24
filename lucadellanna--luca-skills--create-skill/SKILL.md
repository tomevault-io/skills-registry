---
name: create-skill
description: Create a new skill with proper structure. Use when "create a skill", "new skill for", "add a skill", "I need a skill that". Use when this capability is needed.
metadata:
  author: lucadellanna
---

Turn a user's intent into a well-formed skill folder. Elicit the right information through conversation, then generate the skill files.

If `framework/FRAMEWORK.md` is available, use its § "Skill Folder Structure" as the structural reference. Otherwise, use the minimal structure defined here.

---

## 1. Understand intent

Ask the user what the skill should do. Extract:

- **Purpose**: What does it accomplish? (one sentence)
- **Trigger**: When should it activate? (3-5 trigger phrases)
- **Inputs**: What does it need from the user or environment?
- **Outputs**: What does it produce? (files, conversation, changes)

If the user gives a vague description ("a skill for emails"), ask clarifying questions. If they give a detailed brief, confirm your understanding and move on.

## 2. Decide audience and scope

Two questions, asked together or inferred from context:

**Audience** — who will use this skill?

- **Personal**: Just the author. Can be rough, assume the author's context, skip edge cases they'll never hit.
- **Distribution**: Other people will install it. Must handle unfamiliar users: clear trigger phrases in the description, thorough edge case handling, plain language that assumes no shared context.

**Scope** — where does the skill live?

- **Project** (`./skills/<name>/`) — uses project-specific data, templates, or conventions. Default when unclear.
- **Global** (`~/.claude/skills/<name>/`) — generic utility useful across all projects.

These are independent. A personal skill can be global (your own utility across projects). A distribution skill can be project-scoped (shipped with a specific repo).

## 3. Decide complexity

Based on the intent, decide what files the skill needs:

- **Simple** (most skills): Just `SKILL.md`. Procedure, judgment, and output format all fit in one file without being unwieldy.
- **Structured**: `SKILL.md` + `template.md` and/or `criteria.md`. Use when the skill has substantial output formatting (extract to `template.md`) or domain-specific evaluation criteria (extract to `criteria.md`).

Don't over-engineer. Start simple — files can be extracted later via `/skills-improve`.

## 4. Draft the skill

Write `SKILL.md` with:

- **Frontmatter**: `name` (kebab-case, matches folder), `display-name`, `description` (include trigger phrases), `last-updated` (current UTC), `origin: self-authored`.
- **Opening line**: One sentence summarizing what the skill does.
- **Numbered steps** with descriptive headers. Imperative tone ("Do X", not "You should X").
- **Plain language** throughout — no jargon a non-technical user wouldn't understand.
- **Safety**: If the skill modifies files, include preview → confirm → backup → apply. If it reads sensitive data, note that.

**Distribution skills** additionally need:
- Thorough edge case handling — what happens when input is missing, ambiguous, or unexpected? Address the non-happy paths that strangers will hit.
- Description with 3-5 trigger phrases (these drive discovery during installation).
- No assumptions about the user's environment beyond what the skill explicitly checks for.

**Personal skills** can skip these — keep them lean and direct.

If the skill needs `template.md` or `criteria.md`, draft those too. Reference them from `SKILL.md` by section name (e.g., `template.md` § **Section name**).

## 5. Preview and confirm

Show the user:
- The file(s) that will be created and where.
- The full content of each file.

Proceed only after confirmation.

## 6. Create files

Write the skill folder and files. No other files are modified in this step.

## 7. Register the skill

Add a row to the appropriate CLAUDE.md skills table:
- **Project skill** → project's `CLAUDE.md`
- **Global skill** → `~/.claude/CLAUDE.md`

Show the addition and confirm before applying.

## 8. Review

Run `/skills-review` on the new skill. Fix any errors or warnings before finishing.

Report what was created and how to invoke it.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/lucadellanna) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
