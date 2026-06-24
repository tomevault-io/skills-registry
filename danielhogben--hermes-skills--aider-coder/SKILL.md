---
name: aider-coder
description: Terminal pair programming with Aider, the AI pair programmer that edits Use when this capability is needed.
metadata:
  author: Danielhogben
---

# Aider Coder

Terminal pair programming with [Aider](https://aider.chat/) — an AI pair programmer that directly edits files in your local git repository.

## Commands

### Initialize a project

```bash
python3 aider_coder.py init [--dir /path/to/project]
```

Detects or creates a git repo, generates `.aider.conf.yml` with sensible defaults, and shows project status.

### Run a coding task

```bash
python3 aider_coder.py code "Add error handling to the login function" --files src/auth.py src/utils.py
python3 aider_coder.py code "Refactor the database layer to use connection pooling" --files db/*.py
python3 aider_coder.py code "Fix the bug where null values crash the parser" --all
```

Runs aider with your natural language request against specified files. Use `--files` for specific files or `--all` to let aider auto-detect relevant files.

### Show diffs

```bash
python3 aider_coder.py diff
python3 aider_coder.py diff --staged
python3 aider_coder.py diff --file src/auth.py
```

Shows git diff of changes made by aider (or any changes in the working tree).

### Undo last aider commit

```bash
python3 aider_coder.py undo
```

Reverts the last commit made by aider (aider commits each change set). Useful when a change introduces regressions.

### List / switch models

```bash
python3 aider_coder.py models
python3 aider_coder.py models --set gpt-4o
python3 aider_coder.py models --set claude-sonnet-4-20250514
```

Lists available models or switches the default model in `.aider.conf.yml`.

### Run tests

```bash
python3 aider_coder.py test
python3 aider_coder.py test --cmd "pytest tests/ -x"
python3 aider_coder.py test --cmd "npm test"
```

Runs the project test suite. Auto-detects test runner (pytest, npm test, go test, cargo test, etc.) or uses a custom command.

### Run linting

```bash
python3 aider_coder.py lint
python3 aider_coder.py lint --files src/auth.py
python3 aider_coder.py lint --fix
```

Runs linting on changed files. Auto-detects linter (ruff, flake8, eslint, golangci-lint, etc.) or uses project config.

## Workflow

### Quick coding session

1. Navigate to your project: `cd /path/to/project`
2. Init: `python3 aider_coder.py init`
3. Code: `python3 aider_coder.py code "implement feature X" --files relevant.py`
4. Review: `python3 aider_coder.py diff`
5. Test: `python3 aider_coder.py test`
6. If broken: `python3 aider_coder.py undo`

### Model configuration

Aider supports many models. Set your API keys as environment variables:
- `ANTHROPIC_API_KEY` for Claude models
- `OPENAI_API_KEY` for GPT models
- `OPENROUTER_API_KEY` for OpenRouter models

## Notes

- Aider makes real file edits and git commits — always review diffs before pushing
- Aider respects `.gitignore` and won't edit ignored files by default
- Use `--no-auto-commits` flag to stage changes without committing
- The `.aider.conf.yml` file persists your model and settings preferences

---
> Source: [Danielhogben/hermes-skills](https://github.com/Danielhogben/hermes-skills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-23 -->
