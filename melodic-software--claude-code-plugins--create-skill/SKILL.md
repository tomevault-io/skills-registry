---
name: create-skill
description: Create new skill scaffold with required structure. Use when starting a new skill from scratch or need proper YAML frontmatter template. Use when this capability is needed.
metadata:
  author: melodic-software
---

# Create Skill

Create a new skill scaffold with required structure.

## Skill Name

$ARGUMENTS

## Prerequisites

- Skill name should use kebab-case (e.g., `my-new-skill`)
- Skill names should be noun-phrases describing capability (e.g., `docs-management`, not `manage-docs`)

## Instructions

### Step 1: Validate Input

If `$ARGUMENTS` is empty or invalid:

- Prompt the user for a skill name
- Validate it follows kebab-case convention

### Step 2: Load Skill Development Guidance

Invoke the `claude-ecosystem:skill-development` skill to get:

- Skill naming conventions
- Required YAML frontmatter fields
- Directory structure requirements
- Best practices for skill content

### Step 3: Create Skill Scaffold

Create the directory and SKILL.md:

```bash
mkdir -p .claude/skills/$ARGUMENTS
```

Create `.claude/skills/$ARGUMENTS/SKILL.md` with:

```yaml
---
description: [Brief description of what this skill does]
allowed-tools: [Comma-separated list of tools needed]
---

# [Skill Name]

[Skill content following patterns from skill-development guidance]
```

### Step 4: Follow Established Patterns

Reference existing skills in this repo for:

- Content organization
- Progressive disclosure patterns
- Reference file usage

## Example Usage

```text
/create-skill api-integration
```

Creates:

- `.claude/skills/api-integration/SKILL.md` with proper frontmatter
- Ready for customization with skill-specific content

## Fallback

If `skill-development` skill is unavailable, use these minimum requirements:

**Required frontmatter:**

- `description`: Brief description (required)
- `allowed-tools`: Comma-separated tool list (optional)

**Content structure:**

- H1 title matching skill name
- Overview section
- Instructions or guidance
- Examples where applicable

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/melodic-software) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
