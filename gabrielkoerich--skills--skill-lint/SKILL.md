---
name: skill-lint
description: Lint and auto-fix skill folders for metadata, naming consistency, path placeholder consistency, and optional agents/openai.yaml presence. Use when this capability is needed.
metadata:
  author: gabrielkoerich
---

# Skill Lint

Use this skill to validate and normalize skills in a repository.

## Workflow

1. Run linter in check mode
2. Review findings
3. Run auto-fix mode for safe rewrites
4. Re-run check mode

## Commands

```bash
# Check all skills in current repo
scripts/lint-skills.sh

# Check a specific skills root
scripts/lint-skills.sh ~/Projects/skills

# Auto-fix safe issues
scripts/lint-skills.sh --fix
```

## Checks

- `SKILL.md` exists for each skill directory
- Frontmatter includes `name` and `description`
- `name` matches directory name
- Placeholder consistency: use `<project-name>` only (no mixed placeholder styles)
- `.claude-plugin/plugin.json` exists with `name` and `description`
- `agents/openai.yaml` exists with display name and description

## Publishing Patterns

### Claude Code

```bash
cp -r <skill-dir> ~/.claude/skills/<skill-name>
```

### OpenCode

```bash
cp -r <skill-dir> <workspace>/skills/<skill-name>
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/gabrielkoerich) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
