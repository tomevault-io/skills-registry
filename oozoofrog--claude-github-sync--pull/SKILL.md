---
name: pull
description: Pull latest configuration from GitHub Use when this capability is needed.
metadata:
  author: oozoofrog
---

# Pull Configuration

Pull the latest configuration from your GitHub repository and automatically merge settings.

## Usage
```
/claude-github-sync:pull
```

## Configuration Reference

| Item | Path | Description |
|------|------|-------------|
| **Config file** | `~/.claude/sync-config.json` | Sync configuration (repo URL, setup method) |
| **Git remote** | `~/.claude/.git/config` | Primary check - git origin remote URL |
| **Sync settings** | `~/.claude/settings.sync.json` | Shared settings (synced to GitHub) |
| **Local settings** | `~/.claude/settings.local.json` | Machine-specific settings (gitignored) |
| **Merged output** | `~/.claude/settings.json` | Auto-merged result of sync + local |
| **Merge script** | `~/.claude/scripts/merge-settings.mjs` | Deep merges sync + local → settings.json |

## Instructions

### Step 1: Check git remote (primary check)

The sync is configured if `~/.claude` has a git remote. Config file (`~/.claude/sync-config.json`) is optional fallback.

```bash
cd ~/.claude

# Primary check: git remote
REPO_URL=$(git remote get-url origin 2>/dev/null)
if [ -n "$REPO_URL" ]; then
    echo "✅ Remote: $REPO_URL"
else
    # Fallback: check config file
    CONFIG_FILE="$HOME/.claude/sync-config.json"
    if [ -f "$CONFIG_FILE" ]; then
        echo "⚠️  Config exists but no git remote"
        echo "Run: /claude-github-sync:setup to fix"
        exit 1
    else
        echo "❌ Not configured"
        echo ""
        echo "Run: /claude-github-sync:setup"
        exit 1
    fi
fi
```

### Step 2: Pull changes
```bash
cd ~/.claude

# Get current branch
BRANCH=$(git rev-parse --abbrev-ref HEAD 2>/dev/null || echo "main")

# Save current HEAD for change detection
HEAD_BEFORE=$(git rev-parse HEAD 2>/dev/null)

# Stash local changes if any
STASHED=false
if ! git diff --quiet 2>/dev/null; then
    echo "Stashing local changes..."
    git stash
    STASHED=true
fi

# Pull with rebase
if git pull --rebase origin "$BRANCH" 2>&1; then
    echo "✅ Pulled latest configuration"
else
    echo "❌ Pull failed"
    echo ""
    echo "Possible issues:"
    echo "  - Network connection"
    echo "  - Merge conflicts (run: git status)"
    echo "  - Remote branch doesn't exist"
    git status --short
fi

# Restore stashed changes
if [ "$STASHED" = true ]; then
    echo "Restoring local changes..."
    git stash pop
fi

# Show what changed
HEAD_AFTER=$(git rev-parse HEAD 2>/dev/null)
if [ "$HEAD_BEFORE" != "$HEAD_AFTER" ]; then
    echo ""
    echo "📋 Changes pulled:"
    echo ""
    echo "--- New commits ---"
    git log --oneline "$HEAD_BEFORE..$HEAD_AFTER"
    echo ""
    echo "--- Files changed ---"
    git diff --stat "$HEAD_BEFORE..$HEAD_AFTER"
else
    echo ""
    echo "📋 No new changes from remote."
fi
```

### Step 3: Merge settings
After pulling, automatically merge sync and local settings.

The merge script can be in multiple locations:
1. `~/.claude/scripts/merge-settings.mjs` (user installed)
2. Plugin's scripts directory (bundled)

```bash
cd ~/.claude

# Find merge script in multiple locations
MERGE_SCRIPT=""

# Location 1: User's scripts directory
if [ -f "$HOME/.claude/scripts/merge-settings.mjs" ]; then
    MERGE_SCRIPT="$HOME/.claude/scripts/merge-settings.mjs"
fi

# Location 2: Plugin cache (find latest version)
if [ -z "$MERGE_SCRIPT" ]; then
    PLUGIN_SCRIPT=$(find "$HOME/.claude/plugins/cache/claude-sync/claude-github-sync" -name "merge-settings.mjs" 2>/dev/null | head -1)
    if [ -n "$PLUGIN_SCRIPT" ]; then
        MERGE_SCRIPT="$PLUGIN_SCRIPT"
    fi
fi

# Location 3: Marketplace source
if [ -z "$MERGE_SCRIPT" ]; then
    MARKETPLACE_SCRIPT="$HOME/.claude/plugins/marketplaces/claude-sync/scripts/merge-settings.mjs"
    if [ -f "$MARKETPLACE_SCRIPT" ]; then
        MERGE_SCRIPT="$MARKETPLACE_SCRIPT"
    fi
fi

# Run merge if found
if [ -n "$MERGE_SCRIPT" ]; then
    echo ""
    echo "Merging settings..."
    node "$MERGE_SCRIPT"
else
    echo ""
    echo "⚠️  merge-settings.mjs not found"
    echo "   Settings will not be auto-merged"
fi
```

### Step 4: Report result
Show the user what was updated.

```bash
echo ""
echo "📊 Current settings files:"
ls -la ~/.claude/settings*.json 2>/dev/null | awk '{print "  " $9 " (" $5 " bytes)"}'
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/oozoofrog) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
