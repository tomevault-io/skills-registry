---
name: setup
description: Interactive setup wizard for GitHub sync Use when this capability is needed.
metadata:
  author: oozoofrog
---

# Interactive Setup

Set up GitHub sync with guided configuration. Handles everything automatically including repository creation and settings sync.

## Usage
```
/claude-github-sync:setup
```

## Configuration Reference

This setup creates the following files:

| Item | Path | Description |
|------|------|-------------|
| **Config file** | `~/.claude/sync-config.json` | Sync configuration (repo URL, setup timestamp, method) |
| **Git repo** | `~/.claude/.git/` | Git repository initialized in ~/.claude |
| **Git remote** | `origin` → GitHub repo | Primary sync check (git remote URL) |
| **Sync settings** | `~/.claude/settings.sync.json` | Shared settings (synced to GitHub) |
| **Local settings** | `~/.claude/settings.local.json` | Machine-specific settings (gitignored) |
| **Merged output** | `~/.claude/settings.json` | Auto-merged result of sync + local |
| **Merge script** | `~/.claude/scripts/merge-settings.mjs` | Deep merges sync + local → settings.json |
| **Git ignore** | `~/.claude/.gitignore` | Excludes cache, session data, local settings |

### Config file format (`sync-config.json`):
```json
{
  "repo": "https://github.com/<username>/claude-config.git",
  "configuredAt": "2026-01-26T...",
  "setupMethod": "interactive"
}
```

## Instructions

### Step 1: Check prerequisites

```bash
echo "Claude GitHub Sync Setup"
echo "==========================="
echo ""

# Check if gh CLI is installed
if ! command -v gh &>/dev/null; then
    echo "GitHub CLI (gh) is required but not installed"
    echo ""
    echo "Install GitHub CLI:"
    echo "  macOS:  brew install gh"
    echo "  Linux:  sudo apt install gh  (or see https://cli.github.com/)"
    echo "  Windows: winget install GitHub.cli"
    echo ""
    echo "After installing, run: gh auth login"
    echo "Then retry: /claude-github-sync:setup"
    exit 1
fi
echo "GitHub CLI installed"

# Check authentication status (github.com only)
if ! gh auth status --hostname github.com &>/dev/null 2>&1; then
    echo "Not authenticated with GitHub"
    echo ""
    echo "Run this command to authenticate:"
    echo "  gh auth login"
    echo ""
    echo "Then retry: /claude-github-sync:setup"
    exit 1
fi
echo "Authenticated with GitHub"
gh auth status --hostname github.com 2>&1 | grep "Logged in" | head -1
```

### Step 2: Get GitHub username

```bash
USERNAME=$(gh api user --jq '.login' 2>/dev/null)
if [ -z "$USERNAME" ]; then
    echo "Failed to get GitHub username"
    exit 1
fi
echo ""
echo "GitHub user: $USERNAME"
```

### Step 3: Repository setup

```bash
DEFAULT_REPO="claude-config"
echo ""
echo "Repository Setup"
echo "-------------------"

# Check if default repo already exists
if gh repo view "$USERNAME/$DEFAULT_REPO" &>/dev/null 2>&1; then
    echo "Found existing repo: $USERNAME/$DEFAULT_REPO"
    REPO_URL="https://github.com/$USERNAME/$DEFAULT_REPO.git"
else
    echo "Creating private repository: $USERNAME/$DEFAULT_REPO"
    if gh repo create "$DEFAULT_REPO" --private --description "Claude Code configuration sync" 2>/dev/null; then
        REPO_URL="https://github.com/$USERNAME/$DEFAULT_REPO.git"
        echo "Created: $REPO_URL"
    else
        echo "Failed to create repository"
        echo ""
        echo "You can create it manually at: https://github.com/new"
        echo "Then run: /claude-github-sync:init <repo-url>"
        exit 1
    fi
fi
```

### Step 4: Save configuration

```bash
CONFIG_FILE="$HOME/.claude/sync-config.json"

cat > "$CONFIG_FILE" << EOF
{
  "repo": "$REPO_URL",
  "configuredAt": "$(date -Iseconds)",
  "setupMethod": "interactive"
}
EOF
echo "Configuration saved"
```

### Step 5: Initialize git repository

```bash
cd ~/.claude

if [ -d ".git" ]; then
    git remote set-url origin "$REPO_URL" 2>/dev/null || git remote add origin "$REPO_URL"
    echo "Updated git remote"
else
    git init -q
    git remote add origin "$REPO_URL"
    echo "Initialized git repository"
fi
```

### Step 6: Ensure .gitignore has required entries

```bash
# Required gitignore entries for claude-github-sync
REQUIRED_ENTRIES=(
    "# Cache (auto-reinstalled)"
    "plugins/cache/"
    "plugins/marketplaces/"
    "plugins/install-counts-cache.json"
    "plugins/known_marketplaces.json"
    "cache/"
    "paste-cache/"
    "stats-cache.json"
    ""
    "# Local settings (machine-specific)"
    "settings.json"
    "settings.local.json"
    ""
    "# Authentication & Credentials (SECURITY)"
    "*token*"
    "*credential*"
    "*auth*"
    "*.pem"
    "*.key"
    "*secret*"
    ".env"
    ".env.*"
    "api-key*"
    ""
    "# Conversation transcripts (privacy)"
    "transcripts/"
    "conversation*/"
    "chat-history/"
    ""
    "# Session data"
    "session-env/"
    "debug/"
    ".session-stats.json"
    "history.jsonl"
    "file-history/"
    "projects/"
    "shell-snapshots/"
    "tasks/"
    ""
    "# Telemetry"
    "statsig/"
    "telemetry/"
    ""
    "# IDE"
    "ide/"
    ""
    "# System"
    ".DS_Store"
    "*.bak"
    ""
    "# Usage stats (machine-specific)"
    "plugins/*/.usage-cache.json"
    ""
    "# Plans and todos"
    "plans/"
    "todos/"
    ""
    "# OMC state"
    ".omc/"
)

if [ ! -f ".gitignore" ]; then
    # Create new .gitignore
    printf '%s\n' "${REQUIRED_ENTRIES[@]}" > .gitignore
    echo "Created .gitignore"
else
    # Merge missing entries into existing .gitignore
    ADDED=0
    for entry in "${REQUIRED_ENTRIES[@]}"; do
        # Skip empty lines and comments for checking
        if [[ -n "$entry" && ! "$entry" =~ ^# ]]; then
            if ! grep -qxF "$entry" .gitignore 2>/dev/null; then
                echo "$entry" >> .gitignore
                ((ADDED++))
            fi
        fi
    done
    if [ $ADDED -gt 0 ]; then
        echo "Added $ADDED missing entries to .gitignore"
    else
        echo ".gitignore already complete"
    fi
fi

# Remove tracked files that should be ignored
for file in settings.json settings.local.json plugins/known_marketplaces.json; do
    if git ls-files --error-unmatch "$file" &>/dev/null 2>&1; then
        git rm --cached "$file" 2>/dev/null
        echo "Untracked: $file (now ignored)"
    fi
done
```

### Step 7: Setup settings sync files

Create the settings sync infrastructure:
- `settings.sync.json` - Shared settings (git tracked)
- `settings.local.json` - Local-only settings (gitignored)
- `scripts/merge-settings.mjs` - Merge script

```bash
cd ~/.claude

# Create scripts directory
mkdir -p scripts

# Create merge-settings.mjs if it doesn't exist
if [ ! -f "scripts/merge-settings.mjs" ]; then
    cat > "scripts/merge-settings.mjs" << 'SCRIPT_EOF'
#!/usr/bin/env node
/**
 * Merge settings.sync.json and settings.local.json into settings.json
 * Local settings override sync settings (deep merge)
 */

import { readFileSync, writeFileSync, existsSync } from 'fs';
import { join } from 'path';
import { homedir } from 'os';

const CLAUDE_DIR = join(homedir(), '.claude');
const SYNC_FILE = join(CLAUDE_DIR, 'settings.sync.json');
const LOCAL_FILE = join(CLAUDE_DIR, 'settings.local.json');
const OUTPUT_FILE = join(CLAUDE_DIR, 'settings.json');

/**
 * Deep merge two objects. Source values override target values.
 */
function deepMerge(target, source) {
  const result = { ...target };

  for (const key of Object.keys(source)) {
    const sourceValue = source[key];
    const targetValue = result[key];

    if (
      sourceValue !== null &&
      typeof sourceValue === 'object' &&
      !Array.isArray(sourceValue) &&
      targetValue !== null &&
      typeof targetValue === 'object' &&
      !Array.isArray(targetValue)
    ) {
      result[key] = deepMerge(targetValue, sourceValue);
    } else {
      result[key] = sourceValue;
    }
  }

  return result;
}

function loadJson(filePath) {
  if (!existsSync(filePath)) {
    return {};
  }
  try {
    const content = readFileSync(filePath, 'utf-8');
    return JSON.parse(content);
  } catch (error) {
    console.error(`Failed to parse ${filePath}: ${error.message}`);
    process.exit(1);
  }
}

function main() {
  // Load settings
  const syncSettings = loadJson(SYNC_FILE);
  const localSettings = loadJson(LOCAL_FILE);

  // Merge: local overrides sync
  const merged = deepMerge(syncSettings, localSettings);

  // Write output
  const output = JSON.stringify(merged, null, 2);
  writeFileSync(OUTPUT_FILE, output + '\n', 'utf-8');

  console.log(`Merged ${Object.keys(syncSettings).length} sync + ${Object.keys(localSettings).length} local keys into settings.json`);
}

main();
SCRIPT_EOF
    chmod +x "scripts/merge-settings.mjs"
    echo "Created scripts/merge-settings.mjs"
fi

# Create settings.sync.json if it doesn't exist
if [ ! -f "settings.sync.json" ]; then
    # Extract syncable settings from existing settings.json
    if [ -f "settings.json" ]; then
        # Use node to extract sync-safe settings
        node -e "
        const fs = require('fs');
        const settings = JSON.parse(fs.readFileSync('settings.json', 'utf-8'));

        // Keys that should be synced (not machine-specific)
        const syncKeys = ['enabledPlugins', 'includeCoAuthoredBy', 'hooks', 'permissions'];
        const syncSettings = {};

        for (const key of syncKeys) {
            if (settings[key] !== undefined) {
                syncSettings[key] = settings[key];
            }
        }

        // Remove machine-specific permission patterns
        if (syncSettings.permissions?.allow) {
            syncSettings.permissions.allow = syncSettings.permissions.allow.filter(p =>
                !p.includes('/Users/') && !p.includes('/home/')
            );
        }

        fs.writeFileSync('settings.sync.json', JSON.stringify(syncSettings, null, 2) + '\n');
        console.log('Created settings.sync.json from existing settings');
        " 2>/dev/null || echo '{}' > settings.sync.json
    else
        echo '{}' > settings.sync.json
        echo "Created empty settings.sync.json"
    fi
fi

# Create settings.local.json if it doesn't exist
if [ ! -f "settings.local.json" ]; then
    # Extract local-only settings from existing settings.json
    if [ -f "settings.json" ]; then
        node -e "
        const fs = require('fs');
        const settings = JSON.parse(fs.readFileSync('settings.json', 'utf-8'));

        // Keys that should stay local (machine-specific)
        const localKeys = ['statusLine'];
        const localSettings = {};

        for (const key of localKeys) {
            if (settings[key] !== undefined) {
                localSettings[key] = settings[key];
            }
        }

        fs.writeFileSync('settings.local.json', JSON.stringify(localSettings, null, 2) + '\n');
        console.log('Created settings.local.json from existing settings');
        " 2>/dev/null || echo '{}' > settings.local.json
    else
        echo '{}' > settings.local.json
        echo "Created empty settings.local.json"
    fi
fi

echo "Settings sync configured"
```

### Step 8: Initial push

```bash
echo ""
echo "Pushing initial configuration..."

git add -A
if git diff --cached --quiet; then
    echo "Repository already in sync"
else
    git commit -m "Initial Claude Code configuration sync"

    BRANCH=$(git rev-parse --abbrev-ref HEAD 2>/dev/null || echo "main")
    if git push -u origin "$BRANCH" 2>&1; then
        echo "Pushed to GitHub ($BRANCH)"
    else
        echo "Push failed - you may need to pull first if repo has existing content"
        echo "   Run: /claude-github-sync:pull"
    fi
fi
```

### Step 9: Show completion message

```
Setup complete!
==================

Your configuration is now synced to:
  https://github.com/$USERNAME/claude-config

Files:
  settings.sync.json  - Shared settings (synced to GitHub)
  settings.local.json - Local settings (machine-specific, not synced)
  settings.json       - Merged output (auto-generated)

Commands:
  /claude-github-sync:push   - Push local changes to GitHub
  /claude-github-sync:pull   - Pull latest from GitHub (auto-merges settings)
  /claude-github-sync:status - Check sync status

On other machines:
  /claude-github-sync:setup  - Run this again (uses existing repo)
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/oozoofrog) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
