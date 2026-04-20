---
name: skill-author
description: Create or update Codex/Claude skills with a shared SKILL.md source of truth; use when asked to add a new skill, refine a skill, or set up shared skill scaffolding. Use when this capability is needed.
metadata:
  author: d3ming
---

# Skill Author

## Inputs
- Skill intent, example triggers, and any needed resources (scripts, references, assets).
- Scope: **system** (shared across all projects) or **project** (this project only; user provides target dir).
- Optional: target agent(s) and desired name.

## Workflow

### 0. Determine scope
Ask if not specified:
- **System**: skill lives in `~/projects/dot-agents/master/skills/`, symlinked into agent dirs. Available everywhere.
- **Project**: skill lives in `<project-root>/.claude/skills/` and/or `<project-root>/.codex/skills/`. Available in that project only. No symlinks needed.

### 0.1 Source-of-truth rule (system skills)
- All **system-level** skills are managed from `~/projects/dot-agents/master/skills/`.
- Archived or penalty-box system skills live in `~/projects/dot-agents/master/skills-archive/` and should not be symlinked into agent-visible skill directories.
- Treat `~/.agents/skills/` as a convenience mirror only (it may be a symlink to dot-agents). Do not use it as the canonical authoring path.
- Agent-visible installs (`~/.codex/skills/*`, `~/.openclaw/skills/*`, or other client skill dirs) should be symlinks pointing back to dot-agents.

### System-level skill
1. Scaffold: from `~/projects/dot-agents/`, run:
   ```
   make new-skill name=<name>
   ```
   This creates `master/skills/<name>/SKILL.md` with frontmatter template and wires symlinks into `claude/.claude/skills/` and `codex/.codex/skills/`.
2. Define name and triggers
   - Lowercase, digits, hyphens; under 64 chars.
   - Description captures when to use it (primary trigger signal).
   - Decide trigger tier up front:
     - **Auto**: normal description only
     - **Explicit-only**: add `disable-model-invocation: true`
     - **Penalty box**: store outside `master/skills/` until rewritten
3. Write SKILL.md body — imperative voice, concise, include guardrails and failure cases.
4. Add resources if needed under the same folder (`scripts/`, `references/`, `assets/`).
   - Reference via absolute path: `~/projects/dot-agents/master/skills/<name>/scripts/...`
5. Optionally add Gemini wrapper: run `make build` to auto-generate from the skill.
6. Sanity check: `ls -la claude/.claude/skills/<name> codex/.codex/skills/<name>`; SKILL.md under ~500 lines.

> **Manual wiring** (if not using `make new-skill`): run `scripts/link-skill.sh <name>` from repo root. To relink all skills: `make build`.

### Project-level skill
1. Define skill name and triggers (same rules as above).
2. Create skill directly in the project:
   - Claude: `<project-root>/.claude/skills/<name>/SKILL.md`
   - Codex: `<project-root>/.codex/skills/<name>/SKILL.md`
   - If both agents are used, keep SKILL.md in one location and symlink the other to it.
3. Write SKILL.md body — same format as system-level.
4. Add resources under the same folder; reference via absolute path from project root.
5. No symlinks into dot-agents needed.

## Guardrails
- Do not add extra docs (README, changelog).
- Avoid long examples; move detail into `references/`.
- Ask before overwriting an existing skill.
- When two skills overlap, keep one active and move the other to `master/skills-archive/` until the trigger boundary is clear.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/d3ming) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
