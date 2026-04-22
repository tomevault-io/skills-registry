---
name: release-note-writer
description: Generate and update release notes for sanhe-claude-code-plugins. Use when user wants to create or update release notes, document version changes, or prepare for a new release. Reads git commit history, pyproject.toml version, and follows the standardized format. Use when this capability is needed.
metadata:
  author: machu-gwu
---

# Release Note

Generate and update release notes following the standardized format for this repository.

## Workflow

### Step 1: Gather Information

1. Read `references/format-standard.md` to understand the release note format
2. Get current version from `pyproject.toml` using: `grep "^version" pyproject.toml`
3. Get the last release tag: `git tag -l | tail -1` (if no tags, this is the initial release)
4. Get commit history since last tag:
   - If tag exists: `git log <last-tag>..HEAD --oneline`
   - If no tag: `git log --oneline`

### Step 2: Analyze Changes

1. Review each commit message to identify changes
2. Map changes to the format: `{plugin_name}@{category}@{feature_name}`
3. Categorize each change as: Add, Update, Fix, or Remove
4. Cross-reference with `plugins/` directory structure to verify accuracy

### Step 3: Handle Conflicts

If information from user conflicts with what you read:
- Ask user to confirm the correct information
- User input takes precedence after confirmation

### Step 4: Update release-history.rst

1. Read current `release-history.rst`
2. Update the version entry with:
   - Correct version number from pyproject.toml
   - Today's date in YYYY-MM-DD format
   - Entries grouped by change type (Add, Update, Fix, Remove)
   - Alphabetical order within each group
3. Omit empty change type sections

## Entry Format

```
- {plugin_name}@{category}@{feature_name}: {brief description}
```

### Valid Categories

- `skills` - Agent skills
- `slash-commands` - Slash commands
- `agents` - Custom subagents
- `hooks` - Automation hooks

### Change Types

- **Add** - New additions
- **Update** - Modifications to existing features
- **Fix** - Bug fixes
- **Remove** - Deletions

## Example Output

```rst
0.1.1 (2025-01-13)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

**Add**

- mini-kanban@skills@mini-kanban: file-based task management
- mini-kanban@slash-commands@mini-kanban: kanban command wrapper
- youtube@skills@youtube-video-to-audio: YouTube to audio converter
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/machu-gwu) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
