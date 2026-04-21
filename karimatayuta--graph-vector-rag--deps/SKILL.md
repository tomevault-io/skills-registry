---
name: deps
description: Check, audit, and update Python dependencies Use when this capability is needed.
metadata:
  author: karimatayuta
---

# Dependency Manager

Check, audit, and update Python dependencies.

## Project Setup

- Package manager: `uv`
- Config: `pyproject.toml`
- Lock file: `uv.lock`
- Build system: hatchling
- Python: >=3.13

## Commands Reference

```bash
# Check installed versions
uv pip list

# Check outdated packages
uv pip list --outdated

# Sync dependencies
uv sync
uv sync --extra dev

# Add dependency
uv add <package>
uv add --dev <package>
uv add "<package>>=1.0,<2.0"

# Remove dependency
uv remove <package>

# Update lock file
uv lock

# Update specific package
uv lock --upgrade-package <package> && uv sync

# Update all
uv lock --upgrade && uv sync
```

## Instructions

### "check" or "status" (default)
1. Run `uv pip list` to show installed packages
2. Run `uv pip list --outdated` to show outdated packages
3. Present a summary table of current vs latest versions
4. Highlight packages with major version updates (potential breaking changes)

### "update" or "upgrade"
1. Show what would be updated (`uv pip list --outdated`)
2. Ask user to confirm before proceeding
3. For specific package: `uv lock --upgrade-package <name> && uv sync`
4. For all: `uv lock --upgrade && uv sync`
5. After updating, run `uv run pytest` to verify nothing broke
6. If tests fail, identify which update caused failure and suggest reverting

### "add <package>"
1. Determine if main or dev dependency
2. Run `uv add <package>` or `uv add --dev <package>`
3. Verify in `pyproject.toml`
4. Run `uv run pytest` to verify compatibility

### "remove <package>"
1. Search for imports of the package in the codebase
2. Warn if the package is imported anywhere
3. Run `uv remove <package>`
4. Run `uv run pytest` to verify

### "audit" or "security"
1. Run `uv pip list` to get all packages
2. Check for known vulnerabilities (suggest `pip-audit` if available)
3. Report findings with severity and recommended actions

## Key Considerations

- `torch` is very large (~2GB). Updates should be deliberate.
- `litellm` updates frequently and may introduce breaking changes.
- `sentence-transformers` must remain compatible with the project's embedding model.
- `neo4j` driver version must match the server version (currently 5-community).
- Always run tests after any dependency change.
- `uv.lock` should be committed after dependency changes.

## Rules

- NEVER remove a dependency without checking for usage first
- NEVER update `torch` or `sentence-transformers` without explicit user consent
- Always run tests after dependency changes
- If `uv sync` fails, check Python version compatibility (requires >=3.13)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/karimatayuta) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
