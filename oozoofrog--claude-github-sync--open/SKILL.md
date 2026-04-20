---
name: open
description: Open GitHub sync repository page in browser Use when this capability is needed.
metadata:
  author: oozoofrog
---

# Open GitHub Repository

Open your Claude Code sync repository page in the default browser.

## Usage
```
/claude-github-sync:open
```

## Configuration Reference

| Item | Path | Description |
|------|------|-------------|
| **Config file** | `~/.claude/sync-config.json` | Sync configuration (repo URL, setup method) |
| **Git remote** | `~/.claude/.git/config` | Primary check - git origin remote URL |

## Instructions

```bash
cd ~/.claude

# Get remote URL
REPO_URL=$(git remote get-url origin 2>/dev/null)

if [ -z "$REPO_URL" ]; then
    echo "❌ Not configured"
    echo ""
    echo "Run: /claude-github-sync:setup"
    exit 1
fi

# Convert SSH URL to HTTPS if needed
# git@github.com:user/repo.git -> https://github.com/user/repo
# https://github.com/user/repo.git -> https://github.com/user/repo
HTTPS_URL=$(echo "$REPO_URL" | sed -E 's|git@github.com:|https://github.com/|' | sed 's|\.git$||')

echo "🌐 Opening: $HTTPS_URL"

# Open in default browser (macOS/Linux/WSL)
if command -v open &>/dev/null; then
    open "$HTTPS_URL"
elif command -v xdg-open &>/dev/null; then
    xdg-open "$HTTPS_URL"
elif command -v wslview &>/dev/null; then
    wslview "$HTTPS_URL"
else
    echo ""
    echo "Could not detect browser. Open manually:"
    echo "  $HTTPS_URL"
fi
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/oozoofrog) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
