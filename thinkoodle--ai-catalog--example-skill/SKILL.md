---
name: example-skill
description: Use when working with a template skill demonstrating the expected format for agency skills
metadata:
  author: thinkoodle
---

## Overview

This is an example skill that demonstrates the expected format for agency-created skills. Use this as a template when creating new skills.

## When to Use

Load this skill when you need guidance on:
- Creating new skills for the agency catalog
- Understanding the SKILL.md format
- Testing skill installation in Oodle AI

## Skill Format Requirements

### Frontmatter

Every skill must have YAML frontmatter with these required fields:

- `name`: Lowercase, hyphenated identifier (must match directory name)
- `description`: Brief description (1-1024 characters)

Optional fields:
- `license`: License type (e.g., "MIT", "Proprietary")
- `compatibility`: Should be "opencode"
- `metadata`: Additional key-value pairs

### Body Content

The body should include:

1. **Overview**: What the skill does
2. **When to Use**: Conditions for loading the skill
3. **Instructions**: Detailed guidance for the agent

## Example Usage

When an agent loads this skill, they receive all the content below the frontmatter. Structure your instructions clearly so the agent knows exactly what to do.

## Best Practices

1. Keep skills focused on a single topic
2. Use clear, actionable language
3. Include examples where helpful
4. Update the `description` to be specific enough for agents to choose correctly

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/thinkoodle) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
