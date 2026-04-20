---
name: push
description: Push configuration changes to GitHub Use when this capability is needed.
metadata:
  author: oozoofrog
---

# Push Configuration

Push local configuration changes to GitHub.

## Usage
```
/claude-github-sync:push
/claude-github-sync:push "Custom commit message"
```

## Configuration Reference

| Item | Path | Description |
|------|------|-------------|
| **Config file** | `~/.claude/sync-config.json` | Sync configuration (repo URL, setup method) |
| **Git remote** | `~/.claude/.git/config` | Primary check - git origin remote URL |
| **Sync settings** | `~/.claude/settings.sync.json` | Shared settings (synced to GitHub) |
| **Local settings** | `~/.claude/settings.local.json` | Machine-specific settings (gitignored) |
| **Merged output** | `~/.claude/settings.json` | Auto-merged result of sync + local |

## Instructions

### Step 1: Check git remote (primary check)

The sync is configured if `~/.claude` has a git remote. Config file (`~/.claude/sync-config.json`) is optional fallback.

```bash
cd ~/.claude

# Primary check: git remote
REPO_URL=$(git remote get-url origin 2>/dev/null)
if [ -n "$REPO_URL" ]; then
    echo "✅ Remote configured: $REPO_URL"
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
        echo "Run one of:"
        echo "  /claude-github-sync:setup       - Interactive setup (recommended)"
        echo "  /claude-github-sync:init <url>  - Manual setup with URL"
        exit 1
    fi
fi
```

### Step 2: Verify gh CLI and authentication

```bash
# Check if gh CLI is available (for better error messages)
if command -v gh &>/dev/null; then
    if ! gh auth status --hostname github.com &>/dev/null 2>&1; then
        echo "⚠️  GitHub CLI not authenticated"
        echo "   Run: gh auth login"
        echo ""
        echo "Attempting push anyway (may fail)..."
    fi
fi
```

### Step 3: Verify remote repository exists (optional)

```bash
# REPO_URL already set in Step 1
# Verify remote exists only if gh CLI is available
if command -v gh &>/dev/null; then
    REPO_PATH=$(echo "$REPO_URL" | sed -E 's|.*github.com[:/]||' | sed 's|\.git$||')

    if ! gh repo view "$REPO_PATH" &>/dev/null 2>&1; then
        echo "⚠️  Cannot verify remote: $REPO_PATH"
        echo "   (May still work if repo exists)"
    fi
fi
```

### Step 4: Stage and commit

Extract optional commit message from `$ARGUMENTS`. Default: "Update Claude Code configuration"

```bash
cd ~/.claude

# Get current branch
BRANCH=$(git rev-parse --abbrev-ref HEAD 2>/dev/null || echo "main")

# Stage all changes
git add -A

# Check if there are changes to commit
if git diff --cached --quiet; then
    echo "✅ Nothing to push - already in sync"
    exit 0
fi

# Commit with message
MESSAGE="${ARGUMENTS:-Update Claude Code configuration}"
git commit -m "$MESSAGE"
echo "✅ Committed: $MESSAGE"
```

### Step 5: Push to remote

```bash
# Push with upstream tracking
if git push -u origin "$BRANCH" 2>&1; then
    echo "✅ Pushed to GitHub ($BRANCH)"
else
    echo ""
    echo "❌ Push failed"
    echo ""
    echo "Troubleshooting:"
    echo "  1. Check authentication: gh auth status"
    echo "  2. Pull first if remote has changes: /claude-github-sync:pull"
    echo "  3. Check network connection"
    echo ""
    echo "For detailed git error, run:"
    echo "  cd ~/.claude && git push -u origin $BRANCH"
    exit 1
fi
```

### Step 6: Show summary

Show what was pushed, including file changes and commit details.

```bash
echo ""
echo "📊 Push Summary:"
echo ""
echo "--- Commit ---"
git log --oneline -1
echo ""
echo "--- Files changed ---"
git diff --stat HEAD~1..HEAD 2>/dev/null || echo "  (first commit)"
echo ""
echo "Remote: $REPO_URL"
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/oozoofrog) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
