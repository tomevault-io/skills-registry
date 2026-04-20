---
name: create-skill
description: Scaffold a new Claude Code skill with proper structure. Use when the user wants to create a new skill, add a skill, or scaffold a SKILL.md. Creates the skill directory and generates a well-structured SKILL.md file. Use when this capability is needed.
metadata:
  author: jacobh
---

# Create Skill

Scaffolds a new Claude Code skill by creating the directory structure and SKILL.md file.

User arguments: `$ARGUMENTS`

## Process

### 1. Parse arguments

Check if the user provided arguments. Arguments can be:
- Just a name: `my-skill`
- Name and description: `my-skill "Description of what it does"`

If no arguments provided, proceed to guided mode.

### 2. Gather information (if not from arguments)

If name or description are missing, ask the user using AskUserQuestion:

**Skill name** - Must be:
- Lowercase letters, numbers, and hyphens only
- Maximum 64 characters
- Cannot contain "anthropic" or "claude"

**Description** - Should include:
- What the skill does
- When Claude should trigger it (trigger phrases)

### 3. Ask for location

Always ask where to create the skill:

| Option | Path | Use case |
|--------|------|----------|
| Personal (global) | `~/.claude/skills/<name>/` | Available across all projects |
| Project-specific | `.claude/skills/<name>/` | Version controlled with the project |

### 4. Validate

Before creating, check:
- Directory doesn't already exist
- Name follows naming rules
- Description is non-empty and under 1024 characters

If validation fails, inform the user and ask for corrections.

### 5. Create the skill

Create the directory and write the SKILL.md file:

```bash
mkdir -p <location>/<skill-name>
```

Write SKILL.md with this structure:

```markdown
---
name: <skill-name>
description: <description>
---

# <Skill Title>

<Brief description of what this skill does>

## Process

1. [First step]
2. [Second step]
3. [...]

## Guidelines

- [Key guideline 1]
- [Key guideline 2]
```

### 6. Confirm creation

Tell the user:
- The full path to the created SKILL.md
- Remind them to edit the file to add their specific instructions
- How to invoke it: `/<skill-name>` or `/<skill-name> arguments`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jacobh) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
