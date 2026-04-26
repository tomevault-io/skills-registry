---
name: skills-sync
description: Synchronizes AI agent skills from local directories and Git repositories, generating Use when this capability is needed.
metadata:
  author: sfc-gh-dflippo
---

# Skills Sync

## Overview

Synchronizes AI agent skills from local directories and Git repositories, generating
`.cursor/rules/skills.mdc` for Cursor IDE integration.

## Usage

**First time setup** (installs uv if needed, then installs skills-sync as a tool):

```bash
python3 .claude/skills/skills-sync/scripts/skills_sync.py
```

**Subsequent runs:**

```bash
skills-sync
```

The script auto-detects the project root by walking up the directory tree.

## Skill Locations (Precedence Order)

| Priority    | Location                      | Type    |
| ----------- | ----------------------------- | ------- |
| 1 (highest) | `$PROJECT/.cortex/skills/`    | Project |
| 2           | `$PROJECT/.claude/skills/`    | Project |
| 3           | `~/.snowflake/cortex/skills/` | Global  |
| 4 (lowest)  | `~/.claude/skills/`           | Global  |

Higher precedence locations override skills with the same name from lower locations.

## Repository Configuration

Place `repos.txt` in any skill directory to sync skills from Git repositories:

```text
https://github.com/anthropics/skills
https://github.com/your-org/your-skills-repo
# Comments start with #
```

The script checks all four skill locations for `repos.txt` files and deduplicates URLs.

### Repository Skill Extraction

Skills are extracted ONLY from `.cortex/skills/*/SKILL.md` and `.claude/skills/*/SKILL.md` paths
within repositories. Extracted skills are placed in `~/.snowflake/cortex/skills/` with a repo prefix
(e.g., `skills-dbt-core/`).

## Managing Repositories

**Add Repository:** Add URL to `repos.txt`, run `skills-sync`

**Remove Repository:** Delete URL from `repos.txt`, run `skills-sync`, optionally delete extracted
skills from `~/.snowflake/cortex/skills/<repo>-<skill>/`

## Output

The script generates `.cursor/rules/skills.mdc` containing `<available_skills>` XML that Cursor
loads automatically for all AI interactions.

## Sync Process

1. Read `repos.txt` from all locations, deduplicate URLs
2. Clone/update repositories to `~/.snowflake/.cache/repos/`
3. Extract skills to `~/.snowflake/cortex/skills/` with repo prefix
4. Scan all four skill locations with precedence rules
5. Validate skills using Agent Skills CLI
6. Generate `.cursor/rules/skills.mdc` with embedded XML
7. Clean up old marker-delimited sections from `AGENTS.md`

## Requirements

- Python 3.8+
- uv (auto-installed when running as script)
- Git (auto-installed if missing)

When run as a Python script, it auto-installs uv, then installs itself as a uv tool. Git is
auto-installed if missing using the appropriate method for your platform.

## Troubleshooting

**Skills not appearing:** Verify SKILL.md exists in immediate child directory with valid
frontmatter:

```yaml
---
name: my-skill
description: What this skill does and when to use it
---
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/sfc-gh-dflippo) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
