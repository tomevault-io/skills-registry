---
name: api-claude-sync
description: Scans the .claude/ directory and updates CLAUDE.md to reflect current skills, rules, and agents Use when this capability is needed.
metadata:
  author: rimthan-lab
---

## Purpose

Scans the `.claude/` directory and updates `CLAUDE.md` to reflect current skills, rules, and agents. This keeps documentation in sync with actual files.

## When to Use

- After adding new skills to `.claude/skills/`
- After adding new rules to `.claude/rules/`
- After adding new agents to `.claude/agents/`
- When CLAUDE.md tables are out of sync with .claude/ directory

## What It Does

1. **Scans directories**
   - `.claude/skills/` - All skill files
   - `.claude/rules/` - All rule files

2. **Parses YAML frontmatter** from each file
   - `name` - Skill/rule identifier
   - `description` - Brief description
   - Other metadata as available

3. **Updates CLAUDE.md tables**
   - Available Skills section
   - Available Rules section

4. **Preserves structure**
   - Maintains table format
   - Keeps priority organization
   - Preserves other sections

## Output Format

```
Scanning .claude/ directory...
Found 28 skills
Found 13 rules

Updating CLAUDE.md tables...
✓ Updated Available Skills table
✓ Updated Available Rules table

Done. CLAUDE.md is now in sync with .claude/ directory.
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/rimthan-lab) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
