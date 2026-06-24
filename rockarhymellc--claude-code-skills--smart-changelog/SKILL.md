---
name: smart-changelog
description: Generates changelogs and release notes from git history. Categorizes commits, handles semantic versioning, supports conventional and non-conventional commits. Creates GitHub Releases. Use when the user says /smart-changelog, asks about release notes, or wants to version a release.
license: MIT
compatibility: Requires git (gh CLI optional for GitHub Releases)
metadata:
  author: bmobot
  version: "1.0"
---

# Smart Changelog Generator

Generate professional changelogs and release notes from your git history.

## When to Activate

- User says `/smart-changelog`, "generate changelog", "release notes", or "what changed"
- User wants to prepare a release
- User asks about version bumping or semantic versioning

## Instructions

### Step 1: Determine the Range

Figure out what commits to include:

```bash
# List recent tags
git tag --sort=-version:refname | head -10

# Find the latest tag
git describe --tags --abbrev=0 2>/dev/null || echo "no tags"

# If no tags, use the first commit
git rev-list --max-parents=0 HEAD
```

**Range logic:**
- If user specifies a range (e.g., "since v1.2.0"): use that
- If tags exist: use `<latest-tag>..HEAD`
- If no tags: use all commits (first release)

Ask the user if unclear:
- "I see the latest tag is `v1.3.0`. Should I generate changes since then?"
- "No tags found — should I include all commits, or specify a starting point?"

### Step 2: Gather Commits

```bash
# Full commit log with hash, subject, body, and author
git log <range> --format='%H|%s|%b|%an' --no-merges

# Or with merge commits if the project uses merge-based workflow
git log <range> --format='%H|%s|%b|%an' --first-parent
```

Also check for:
```bash
# Any breaking changes?
git log <range> --grep='BREAKING' --format='%H %s'

# Linked issues/PRs?
git log <range> --grep='#[0-9]' --format='%H %s'
```

### Step 3: Categorize Commits

#### If the project uses Conventional Commits:

Parse the type prefix to categorize:

| Type | Category | Emoji |
|------|----------|-------|
| `feat` | Added | ✨ |
| `fix` | Fixed | 🐛 |
| `perf` | Performance | ⚡ |
| `refactor` | Changed | ♻️ |
| `docs` | Documentation | 📚 |
| `test` | Tests | 🧪 |
| `chore` | Maintenance | 🔧 |
| `ci` | CI/CD | 🏗️ |
| `style` | Style | 🎨 |
| `revert` | Reverted | ⏪ |
| `BREAKING CHANGE` | Breaking | 💥 |

#### If NOT using Conventional Commits:

Analyze each commit's diff and message to categorize:

```bash
# Get the diff stat for context
git show --stat <hash>
```

Use these heuristics:
- New files created → likely "Added"
- Test files changed → likely "Tests"
- Config/CI files → likely "Maintenance"
- `fix`, `bug`, `patch`, `resolve` in message → "Fixed"
- `add`, `new`, `implement`, `introduce` → "Added"
- `update`, `improve`, `enhance`, `refactor` → "Changed"
- `remove`, `delete`, `deprecate`, `drop` → "Removed"
- `docs`, `readme`, `comment` → "Documentation"
- `perf`, `optimize`, `speed`, `cache` → "Performance"

### Step 4: Determine Version

If the user hasn't specified a version, suggest one based on semver:

**MAJOR** (X.0.0) — if any:
- Breaking changes found
- Incompatible API changes
- Major feature overhauls

**MINOR** (x.Y.0) — if any:
- New features (`feat` commits)
- New capabilities or endpoints
- Backward-compatible additions

**PATCH** (x.y.Z) — if only:
- Bug fixes
- Documentation updates
- Performance improvements
- Refactoring

Present the suggestion:
```
Based on 3 features and 5 fixes (no breaking changes), I suggest:
  Current: v1.3.2 → Next: v1.4.0

Want to use v1.4.0 or specify a different version?
```

### Step 5: Generate Changelog

Use [Keep a Changelog](https://keepachangelog.com/) format:

```markdown
## [1.4.0] - 2026-02-14

### Added
- Rate limiting for API endpoints (#123)
- Export to CSV from dashboard
- Dark mode support

### Fixed
- Login redirect loop on Safari (#456)
- Memory leak in WebSocket handler
- Timezone offset in scheduled reports

### Changed
- Upgraded auth library to v3.0
- Refactored database connection pooling

### Performance
- 40% faster search with new index strategy
- Lazy-load dashboard widgets

### Documentation
- Added API reference for /webhooks endpoint
- Updated deployment guide for Docker
```

**Formatting rules:**
- Group by category (Added, Fixed, Changed, Removed, Performance, etc.)
- Most important categories first (Breaking → Added → Fixed → Changed)
- Each entry is a single line starting with `- `
- Include issue/PR references when found: `(#123)`
- If a commit has a scope, include it: `**auth**: Add rate limiting`
- Skip trivial commits (merge commits, "WIP", version bumps) unless they contain useful info
- Write entries in past tense for consistency within the changelog, but present tense is also fine — match the project's existing style if a CHANGELOG.md exists

### Step 6: Present and Apply

**Show the changelog first** and ask the user which actions to take:

1. **Update CHANGELOG.md**: Prepend the new section to the existing file (or create it)
2. **Create git tag**: `git tag -a v1.4.0 -m "Release v1.4.0"`
3. **Create GitHub Release**: Use `gh release create` with the changelog as notes
4. **Just show it**: Display without modifying any files

#### Updating CHANGELOG.md

If the file exists, insert the new version after the header:

```bash
# Check existing format
head -20 CHANGELOG.md
```

Preserve the existing format. If creating a new CHANGELOG.md:

```markdown
# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/),
and this project adheres to [Semantic Versioning](https://semver.org/).

## [Unreleased]

## [1.4.0] - 2026-02-14
...
```

#### Creating a GitHub Release

```bash
gh release create v1.4.0 \
  --title "v1.4.0" \
  --notes "$(cat <<'EOF'
<changelog content here>
EOF
)"
```

For pre-releases:
```bash
gh release create v2.0.0-beta.1 --prerelease --title "v2.0.0-beta.1" --notes "..."
```

## Edge Cases

- **Monorepo**: Ask which package/directory to scope the changelog to. Use `git log -- <path>` to filter.
- **No new commits**: Report "No changes since last release" rather than generating an empty changelog.
- **Very large changelog** (100+ commits): Summarize by category with counts, show top 5 most impactful per category, and offer to show the full list.
- **Squash-merged PRs**: The squash commit message often contains PR details — parse these for richer entries.
- **Release candidates**: Support pre-release tags like `v1.4.0-rc.1`.
- **Existing CHANGELOG.md with different format**: Match the existing format rather than forcing Keep a Changelog.

## Tips for Great Changelogs

A changelog is for **humans reading it**, not for machines parsing git log. Every entry should answer: "What does this mean for me as a user/developer?"

Bad: `refactor: extract helper function`
Good: `Improved startup time by lazy-loading configuration`

Bad: `fix: null check in processOrder`
Good: `Fixed crash when processing orders with empty shipping address (#789)`

Transform implementation details into user-facing impact.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/rockarhymellc) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
