---
name: scaffold
description: Project scaffolding — template copy, Claude-powered placeholder filling, git init Use when this capability is needed.
metadata:
  author: seanchiuai
---

# Scaffold

`psi new` copies bundled templates and uses Claude to fill `{{PLACEHOLDER}}` values. Implemented in `src/psi/scaffold.py`.

## Overview

The scaffold flow:
1. Copy `src/psi/template/` tree into target directory
2. Scan all files for `{{PLACEHOLDER}}` patterns
3. Build one prompt with all template contents + user's app description
4. Call `claude -p` (via stdin) to fill every placeholder — output as JSON
5. Parse JSON, write filled files back
6. Generate `prompts/phase-1.md` from filled `development-progress.yaml`
7. `git init` + initial commit

## Template Location

Templates are bundled in the package at `src/psi/template/`. Located at runtime via `importlib.resources` (Python 3.9+ API):

```python
from importlib import resources as importlib_resources

ref = importlib_resources.files("psi") / "template"
```

For editable installs, this resolves to the real filesystem path. The `importlib.resources.files()` API is the modern replacement for the deprecated `pkg_resources`.

## Placeholder Convention

All templates use `{{UPPERCASE_WITH_UNDERSCORES}}` syntax. The scaffold collects all files containing `{{...}}` patterns, excluding:
- Binary files (.png, .jpg, etc.)
- The `skills/create-skill/` directory (its placeholders are instructional, not project-specific)

## Claude Call

Single prompt, single call. The prompt includes all template file contents and asks Claude to return a JSON object: `{"file/path.md": "filled content", ...}`.

Claude's response is parsed with `json.loads()`. If Claude wraps output in markdown code fences, those are stripped first.

## Template Files

| File | Key Placeholders |
|------|-----------------|
| `CLAUDE.md` | `APP_DESCRIPTION_ONE_LINE`, `TECH_STACK_LIST`, `COMMANDS`, `SKILLS_LIST`, `ENV_VARS` |
| `TASKS.md` | `APP_NAME`, task items |
| `docs/PRD.md` | `APP_NAME`, user stories, acceptance criteria, tech stack, data schema |
| `docs/design.md` | Wireframes, component hierarchy, colors, responsive behavior |
| `docs/development-progress.yaml` | Phase definitions with integer keys (1, 2, 3...) |
| `prompts/phase-1.md` | Phase 1 deliverables and criteria (regenerated from YAML after fill) |

## Phase-1 Prompt Generation

After Claude fills the templates, `scaffold.py` reads the filled `development-progress.yaml`, extracts phase 1's definition, and uses `PromptGenerator.generate()` to create `prompts/phase-1.md`. This ensures the prompt matches the actual phase definition.

## Troubleshooting

### Claude returns invalid JSON
- Check for markdown code fences in output — parser handles ```` ```json ``` ```` wrapping
- Very long descriptions may cause Claude to truncate — keep app descriptions concise

### Template files missing from installed package
- `pyproject.toml` sets `include-package-data = true` for git-tracked files
- `[tool.setuptools.package-data]` has explicit globs for the `.claude/` dot-directory tree since `**/*` doesn't reliably traverse dot-directories in setuptools
- Patterns include: `template/.claude/**`, `template/.claude/hooks/*`, `template/.claude/skills/**/*`

## Related Files

- `src/psi/scaffold.py` — Main scaffold logic
- `src/psi/template/` — Bundled template tree
- `src/psi/prompt_generator.py` — Phase-1 prompt generation
- `pyproject.toml` — Package data configuration

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/seanchiuai) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
