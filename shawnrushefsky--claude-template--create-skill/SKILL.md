---
name: create-skill
description: Create new Claude Code skills. Use when the user wants to create a skill, make a skill, add a skill, write a skill, or build a skill for Claude Code. Use when this capability is needed.
metadata:
  author: shawnrushefsky
---

# Creating Claude Code Skills

## Overview

Skills are reusable instruction sets that extend Claude Code's capabilities. They're stored as `SKILL.md` files with YAML frontmatter and Markdown instructions.

## Skill Locations

| Type | Path | Scope |
|------|------|-------|
| Personal | `~/.claude/skills/` | Available across all projects |
| Project | `.claude/skills/` | Shared with team in repository |

## Instructions

When creating a skill:

1. **Determine scope**: Ask if the skill should be personal (`~/.claude/skills/`) or project-level (`.claude/skills/`)

2. **Create directory structure**:
   ```
   .claude/skills/<skill-name>/
   └── SKILL.md
   ```

3. **Write the SKILL.md file** with required frontmatter:
   ```markdown
   ---
   name: skill-name
   description: What this skill does. Use when [trigger conditions].
   ---

   # Skill Name

   ## Instructions
   Step-by-step guidance...

   ## Examples
   Concrete examples...
   ```

## Required Frontmatter Fields

| Field | Description |
|-------|-------------|
| `name` | Lowercase, hyphens allowed, max 64 chars |
| `description` | What it does and when to use it (max 1024 chars) |

## Optional Frontmatter Fields

| Field | Description |
|-------|-------------|
| `allowed-tools` | Comma-separated list of tools (e.g., `Read, Grep, Glob, Bash`) |
| `model` | Specific Claude model to use |

## Naming Rules

- Use only lowercase letters, numbers, and hyphens
- Directory name should match the skill name
- File must be exactly `SKILL.md` (case-sensitive)

## Best Practices

1. **Write trigger-focused descriptions**: Include keywords users would naturally say
2. **Keep SKILL.md under 500 lines**: Use progressive disclosure for detailed content
3. **Include concrete examples**: Show exactly how the skill should behave
4. **Link supporting files**: If you need reference material, put it in separate `.md` files and link from SKILL.md
5. **Bundle utility scripts**: Place helper scripts in a `scripts/` subdirectory

## Example: Simple Skill

```markdown
---
name: commit-message
description: Generate commit messages from staged changes. Use when writing commits or reviewing diffs.
---

# Commit Message Generator

## Instructions

1. Run `git diff --staged` to see changes
2. Create a commit message with:
   - Summary under 50 characters (imperative mood)
   - Blank line
   - Detailed description of what and why

## Example Output

```
Add user authentication middleware

Implement JWT-based authentication that validates tokens
on protected routes. Includes refresh token support and
automatic token renewal.
```
```

## Example: Multi-File Skill

```
api-testing/
├── SKILL.md
├── PATTERNS.md
└── scripts/
    └── mock_server.py
```

**SKILL.md:**
```markdown
---
name: api-testing
description: Test REST APIs with mocking and assertions. Use when testing endpoints or writing API tests.
allowed-tools: Read, Write, Bash
---

# API Testing

## Quick Start
...

For common patterns, see [PATTERNS.md](PATTERNS.md).
```

## After Creating

Remind the user to restart Claude Code or start a new conversation for the skill to be available.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/shawnrushefsky) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
