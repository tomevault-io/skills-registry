---
name: publish-skill
description: | Use when this capability is needed.
metadata:
  author: mahidalhan
---

# Publish Skill to Marketplace

Port skills from any project to the claude-hacks marketplace.

## Usage

```bash
publish-skill <skill-name> [--source PATH] [--dry-run] [--no-push]
```

## What It Does

1. Copies skill to `skills/<name>/skills/<name>/`
2. Updates `.claude-plugin/marketplace.json`
3. Bumps version if exists, adds new entry if not
4. Commits and pushes

## Examples

```bash
publish-skill my-skill
publish-skill my-skill --source /path/to/.claude/skills/my-skill
publish-skill my-skill --dry-run
```

## Pre-Publish Check

Verify SKILL.md has:
- `name:` in frontmatter
- `description:` with USE WHEN / DO NOT USE WHEN

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mahidalhan) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
