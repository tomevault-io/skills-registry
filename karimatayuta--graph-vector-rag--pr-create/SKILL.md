---
name: pr-create
description: Create a well-formatted GitHub pull request Use when this capability is needed.
metadata:
  author: karimatayuta
---

# Create Pull Request

Create a GitHub pull request for the current branch with a structured description.

## Prerequisites

- `gh` CLI must be installed and authenticated
- Current branch must have commits not on the target branch
- Changes must be pushed to remote

## Process

### Step 1: Gather Information

```bash
git branch --show-current
git status -sb
git log main..HEAD --oneline
git diff main...HEAD --stat
git diff main...HEAD
```

### Step 2: Analyze Changes

Categorize all changes across ALL commits (not just the latest):

- **New features**: New files, functions, capabilities
- **Enhancements**: Modifications to existing features
- **Bug fixes**: Error corrections
- **Refactoring**: Code restructuring without behavior change
- **Tests**: New or modified tests
- **Documentation**: README, docstrings, architecture docs
- **Configuration**: pyproject.toml, docker-compose.yml, .env changes

### Step 3: Determine PR Title

- Keep under 70 characters
- Use conventional format: `feat:`, `fix:`, `refactor:`, `docs:`, `test:`, `chore:`
- Describe the WHAT, not the HOW

### Step 4: Write PR Description

Use this template:

```markdown
## Summary
<1-3 bullet points describing key changes and WHY>

## Changes

### Added
- <new files, features, capabilities>

### Changed
- <modifications to existing code>

### Fixed
- <bug fixes>

### Removed
- <deleted files or features>

## Test Plan
- [ ] <specific test commands or manual verification>
- [ ] Unit tests pass: `uv run pytest`
```

### Step 5: Push and Create PR

```bash
git push -u origin $(git branch --show-current)

gh pr create --title "<title>" --body "$(cat <<'EOF'
<PR body>

🤖 Generated with [Claude Code](https://claude.com/claude-code)
EOF
)"
```

### Step 6: Output the PR URL

## Rules

- Target branch is `main` unless user specifies otherwise
- NEVER force push
- NEVER push to main/master directly
- If uncommitted changes exist, warn the user and ask what to do
- If no commits beyond main, inform the user
- Include ALL commits in analysis
- Do not include `.env` or credential files

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/karimatayuta) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
