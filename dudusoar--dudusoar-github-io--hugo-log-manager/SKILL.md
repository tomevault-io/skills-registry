---
name: hugo-log-manager
description: Manage development and content logs for Hugo static sites. Use this skill when working with Hugo websites to track changes, add log entries for code modifications or content updates (posts, tutorials, projects), search logs, or generate change summaries. Automatically detects whether changes are development-related (theme, config, code) or content-related (markdown files in content/). Use when this capability is needed.
metadata:
  author: dudusoar
---

# Hugo Log Manager

Maintain organized logs for Hugo website development and content changes.

## Log Files

This skill maintains two separate log files in your Hugo project's `.claude/` directory:

- **.claude/DEVELOPMENT_LOG.md**: Tracks code changes, theme modifications, configuration updates, feature additions, and bug fixes
- **.claude/CONTENT_LOG.md**: Tracks content updates including new posts, tutorials, projects, photos, and edits

## Quick Start

### Adding Log Entries

Use the `add_log_entry.py` script to add entries with automatic change detection:

```bash
# Auto-detect change type from git status
python .claude/skills/hugo-log-manager/scripts/add_log_entry.py "Added dark mode toggle to header"

# Specify log type explicitly
python .claude/skills/hugo-log-manager/scripts/add_log_entry.py "New blog post about Hugo tips" --type content
python .claude/skills/hugo-log-manager/scripts/add_log_entry.py "Updated PaperMod theme to v7.0" --type dev

# Add tags for better organization
python .claude/skills/hugo-log-manager/scripts/add_log_entry.py "Fixed mobile navigation bug" --tags bug,mobile,ui
```

### Searching Logs

Search across both logs or filter by specific criteria:

```bash
# Search for keyword
python .claude/skills/hugo-log-manager/scripts/search_logs.py "theme"

# Search in specific log
python .claude/skills/hugo-log-manager/scripts/search_logs.py "tutorial" --log content

# Filter by date range
python .claude/skills/hugo-log-manager/scripts/search_logs.py --since 2024-01-01

# Filter by tags
python .claude/skills/hugo-log-manager/scripts/search_logs.py --tags bug,mobile
```

### Generating Summaries

Create summaries of recent changes:

```bash
# Summary of last 7 days
python .claude/skills/hugo-log-manager/scripts/summarize_logs.py

# Summary of last 30 days
python .claude/skills/hugo-log-manager/scripts/summarize_logs.py --days 30

# Summary by type
python .claude/skills/hugo-log-manager/scripts/summarize_logs.py --group-by type
```

## Log Entry Format

Each log entry follows this consistent format:

```markdown
### 2024-01-07 14:30 - [Type]

**Description:** Brief description of the change

**Files affected:**
- path/to/file1.md
- path/to/file2.yaml

**Tags:** tag1, tag2, tag3

---
```

## Change Type Detection

The skill automatically detects change types based on file paths:

**Development changes:**
- Theme files (`themes/`)
- Configuration (`hugo.yaml`, `config.toml`)
- Layouts (`layouts/`)
- Assets (`assets/`, `static/`)
- GitHub workflows (`.github/`)

**Content changes:**
- Posts (`content/posts/`)
- Tutorials (`content/tutorials/`)
- Projects (`content/projects/`)
- Photos (`content/photos/`)
- About page (`content/about/`)

## Initial Setup

When first using this skill on a Hugo site, run:

```bash
python .claude/skills/hugo-log-manager/scripts/add_log_entry.py "Initialized log tracking system" --type dev
```

This creates both log files with proper headers if they don't exist.

## Best Practices

1. **Log immediately after changes**: Add entries right after making changes while details are fresh
2. **Use descriptive messages**: Include enough context for future reference
3. **Tag consistently**: Use consistent tags (bug, feature, content, design, etc.)
4. **Group related changes**: For multiple related changes, create one entry listing all affected files
5. **Review regularly**: Use summaries to review progress and identify patterns

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/dudusoar) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
