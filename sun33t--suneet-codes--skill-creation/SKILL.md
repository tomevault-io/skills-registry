---
name: skill-creation
description: This skill should be used when the user asks to "create a skill", "add a new skill", "write a skill", "make a skill", "build a skill for Claude", or mentions creating Claude Code skills, skill development, or SKILL.md files. Provides guidance for creating well-structured Claude Code skills with proper triggers and progressive disclosure. Use when this capability is needed.
metadata:
  author: sun33t
---

# Creating Claude Code Skills

Consult [references/skill-patterns.md](references/skill-patterns.md) for templates and common mistakes.

## Workflow

### 1. Gather Requirements

If the user has not already specified, ask:
- What functionality should this skill support?
- What phrases would trigger this skill? (e.g., "create X", "set up Y")

Skip questions the user has already answered.

Resource signals:
- Same code needed repeatedly → scripts/
- Detailed docs, schemas, API refs → references/
- Output templates, images → assets/

### 2. Create Structure

Location:
- Project skills (shared with team via git): `.claude/skills/skill-name/`
- Personal skills (user-specific, all projects): `~/.claude/skills/skill-name/`

Default to project skills unless the user requests personal.

Check if skill directory already exists before creating. If it exists, confirm with user before overwriting.

```bash
mkdir -p .claude/skills/skill-name
```

Directory name must match the `name` field in frontmatter.

Only create `references/` subdirectory if the skill needs detailed supplementary content.

### 3. Write SKILL.md

Frontmatter format:
```yaml
---
name: skill-name
description: This skill should be used when the user asks to "phrase 1", "phrase 2", "phrase 3", "phrase 4". Provides guidance for [domain/task].
---
```

Name requirements:
- Lowercase letters, numbers, hyphens only
- Max 64 characters
- Descriptive: `pdf-processing` not `skill1`

Description requirements:
- Third-person: "This skill should be used when..."
- Include 3-5 specific trigger phrases in quotes (exact words users would say)
- Trigger phrases should be action verbs: "create X", "add Y", "set up Z", "configure W"
- State what the skill provides
- Max 1024 characters

Body requirements:
- Imperative form: "Create the file" not "You should create the file"
- Minimum: workflow steps and key instructions (~500 words)
- Target: 1,500-2,000 words for complex skills
- Maximum: 5,000 words (move excess to references/)
- Reference supporting files if they exist: `See [references/patterns.md](references/patterns.md)`

### 4. Add References (If Needed)

Create references only when SKILL.md would exceed ~2,000 words or content is supplementary (not core workflow).

Types:
- `references/*.md` - Detailed patterns, examples, advanced techniques
- `scripts/` - Code >20 lines that would be rewritten identically each time
- `assets/` - Templates, images used in output

Inline code snippets ≤20 lines directly in SKILL.md.

Reference scripts in SKILL.md: `Execute scripts/validate.sh to verify structure.`

Simple skills need only SKILL.md. Do not create empty directories.

### 5. Validate

Required checks:
- Frontmatter has `name` and `description`
- Description includes trigger phrases in third person
- Body uses imperative form throughout
- All referenced files exist
- Skill triggers on expected user queries

## Writing for Claude, Not Humans

Skills are consumed by Claude, not humans. Optimize for token efficiency and machine parsing:

- Terse over explanatory - state what to do, not why
- No conversational filler ("Note that...", "It's important to...", "Remember that...")
- No redundant examples showing the same concept multiple ways
- Labels: "Correct:" / "Wrong:" not "Good example:" / "Bad example:"
- Bullet points over prose paragraphs
- No duplicate content across SKILL.md and references

Wrong (human-optimized):
```
It's important to note that you should always validate input before processing.
This helps prevent errors and ensures data integrity.
```

Correct (Claude-optimized):
```
Validate input before processing.
```

## Imperative Form

Correct:
```
Start by reading the configuration.
Validate input before processing.
Create the directory structure.
```

Wrong:
```
You should start by reading the configuration.
You need to validate input.
You can create the directory structure.
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/sun33t) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
