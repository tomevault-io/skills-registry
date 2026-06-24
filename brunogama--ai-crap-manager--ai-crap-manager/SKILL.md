---
name: ai-crap-manager
description: | Use when this capability is needed.
metadata:
  author: brunogama
---

# AI Crap Manager

Centralized management for AI-generated configuration files across all your projects.

## Problem

AI coding tools (Claude Code, Cursor, Windsurf, Copilot, etc.) generate config directories and files at project roots:
- `.claude/`, `CLAUDE.md`, `.claude/settings.json`
- `.cursor/`, `.cursorules`
- `.windsurf/`, `.windsurfrules`
- `.github/copilot-instructions.md`
- `.aider*`, `.cline/`, `.roo/`, `.codex/`, `.bolt/`, `.devin/`, `.replit/`
- Agent configs, hooks, skills, subagents, MCP configs

These pollute git history, create merge conflicts, and clutter repos.

## Solution

`~/.agent-crap/` is the single source of truth. Each project gets a subdirectory. Files are symlinked back to project roots.

```
~/.agent-crap/
  projects/
    my-app/                    # mirrors ~/Code/my-app AI configs
      .claude/
      CLAUDE.md
      .cursor/
    another-repo/
      .claude/
      CLAUDE.md
  templates/                   # reusable config templates
  snapshots/                   # named point-in-time snapshots
  hooks/                       # auto-sync watcher scripts
  backups/                     # automatic backups before operations
  logs/                        # hook execution logs
  registry.json                # maps project names -> original paths
```

## Setup Script

```bash
ACM="bash ~/.claude/skills/ai-crap-manager/scripts/setup.sh"
# Or after alias-install:
# acm <command>
```

## Quick Start

```bash
$ACM init                          # Create ~/.agent-crap/
$ACM register ~/Code/my-app       # Move configs to store, symlink back
$ACM alias-install                 # Add 'acm' alias to shell
```

## Command Reference

### Core Commands

```bash
$ACM init                    # Initialize ~/.agent-crap/ store
$ACM register [path]         # Register project (default: cwd)
$ACM unregister [path]       # Restore files and remove from registry
$ACM sync [path]             # Verify/fix symlinks, absorb new AI files
$ACM sync-all                # Sync ALL registered projects
$ACM status [path]           # Show tracked files, symlink health, snapshots
$ACM list                    # List all registered projects with metadata
$ACM detect [path]           # Scan which AI tools are in use
$ACM gitignore [path]        # Update .gitignore only
```

### Batch Commands

```bash
$ACM batch-register ~/Code/* # Register multiple projects at once
$ACM scan ~/Code             # Scan directory for repos with AI configs
$ACM adopt ~/Code            # Scan + register all found projects
```

`scan` shows what exists without changing anything. `adopt` does scan + register in one step.

### Template Commands

Templates let you save and reuse AI configs across projects.

```bash
$ACM template list                 # List available templates
$ACM template save <name> [path]   # Save project configs as template
$ACM template apply <name> [path]  # Apply template to a project
$ACM template delete <name>        # Delete a template
$ACM template show <name>          # Inspect template contents
```

**Use cases:**
- Save your Claude Code setup (CLAUDE.md, hooks, agents) as a template
- Apply it to every new project
- Maintain "starter" templates per project type (swift-app, react-app, python-lib)

```bash
# Save your best setup
$ACM template save swift-pro ~/Code/my-polished-app

# Start a new project with it
$ACM template apply swift-pro ~/Code/new-app
```

### Snapshot Commands

Snapshots capture the exact state of a project's AI configs at a point in time. Unlike backups (automatic, time-based), snapshots are named and intentional.

```bash
$ACM snapshot <name> [path]            # Create named snapshot
$ACM snapshot-list [path]              # List snapshots for project
$ACM snapshot-restore <name> [path]    # Restore a snapshot
```

**Use cases:**
- Snapshot before experimenting with new agent configs
- Snapshot "working" state before a major CLAUDE.md rewrite
- Quick rollback when something breaks

```bash
$ACM snapshot before-refactor
# ... experiment with configs ...
$ACM snapshot-restore before-refactor  # oops, go back
```

### Diff & Inspect

```bash
$ACM diff [path]             # Show changes between store and project
$ACM tree [path]             # Tree view of managed files
$ACM stats                   # Global statistics across all projects
```

`diff` catches files that were modified directly in the project instead of through symlinks (e.g., after a git checkout that replaced a symlink with a real file).

`stats` shows: project count, agent popularity, disk usage breakdown.

### Custom File Tracking

Track ANY file or directory beyond the built-in AI patterns. Useful for project-specific configs, prompt libraries, AI training data, or anything else you want centralized.

```bash
$ACM track <file-or-dir> [path]      # Move to store + symlink + gitignore
$ACM untrack <file-or-dir> [path]    # Restore to project + remove from store
$ACM tracked [path]                  # List all tracked files (built-in + custom)
```

**Features:**
- Comma-separated multiple files: `$ACM track "file1,file2,dir1"`
- Glob patterns: `$ACM track "*.prompts"`
- Nested paths: `$ACM track configs/ai/prompts.yaml`
- Auto-registers project if not yet registered
- Auto-adds to .gitignore
- Auto-removes from .gitignore on untrack

**Examples:**
```bash
# Track a custom prompt library
$ACM track prompts/

# Track project-specific AI training data
$ACM track .ai-data,.ai-prompts

# Track a Makefile.ai and custom rules
$ACM track "Makefile.ai,rules.yaml"

# Stop tracking .cursorules (restore to project)
$ACM untrack .cursorules

# See what's tracked (split by built-in vs custom)
$ACM tracked
```

### Auto-Sync Hooks

Install hooks so syncing happens automatically.

```bash
$ACM hook-install [path]     # Install git hooks + optional fswatch/LaunchAgent
$ACM hook-remove [path]      # Remove all installed hooks
```

**What gets installed:**

| Hook | Trigger | Purpose |
|------|---------|---------|
| Git post-checkout | Branch switch | Recreate symlinks after checkout |
| Git post-merge | After merge/pull | Absorb new AI files |
| fswatch watcher | File changes in store | Live sync (optional, if fswatch installed) |
| LaunchAgent (macOS) | WatchPaths on store dir | Background sync daemon |

### Maintenance

```bash
$ACM doctor                  # Health check: registry, symlinks, orphans
$ACM gc                      # Remove old backups (>30 days), orphaned dirs
$ACM export <path.tar.gz>    # Export entire store as portable archive
$ACM import <archive.tar.gz> # Import store from archive
$ACM alias-install           # Add 'acm' alias to .zshrc/.bashrc
```

### Options

```bash
--dry-run       # Show what would happen without doing it
--force         # Skip confirmation prompts
--verbose       # Detailed output
--only <agent>  # Only track specific agent (claude, cursor, etc.)
```

## Tracked File Patterns

| Pattern | Tool |
|---------|------|
| `.claude/` | Claude Code |
| `CLAUDE.md` | Claude Code |
| `.cursor/` | Cursor |
| `.cursorules` | Cursor |
| `.windsurf/` | Windsurf |
| `.windsurfrules` | Windsurf |
| `.github/copilot-instructions.md` | GitHub Copilot |
| `.vscode/ai-instructions.md` | VS Code AI |
| `.aider*` | Aider |
| `.cline/` | Cline |
| `.clinerules` | Cline |
| `.roo/` | Roo |
| `.roomodes` | Roo |
| `.codex/` | Codex |
| `.copilot/` | Copilot |
| `.bolt/` | Bolt |
| `.devin/` | Devin |
| `.replit/` | Replit |
| `.idx/` | IDX |

## Workflows

### First time: register everything at once

```bash
$ACM init
$ACM adopt ~/Code            # Finds and registers all repos with AI configs
$ACM alias-install           # 'acm' shortcut
```

### New project with template

```bash
cd ~/Code/new-project
acm template apply swift-pro # Apply your standard AI config
acm hook-install             # Auto-sync on git operations
```

### Before risky config changes

```bash
acm snapshot stable          # Save current working state
# ... edit CLAUDE.md, add hooks, change agents ...
acm snapshot-restore stable  # Nope, go back
```

### Moving to a new machine

```bash
# Old machine:
acm export ~/Desktop/ai-configs.tar.gz

# New machine:
acm import ~/Desktop/ai-configs.tar.gz
acm sync-all                 # Recreate all symlinks
```

### Periodic maintenance

```bash
acm doctor                   # Check health
acm gc                       # Clean old backups
acm stats                    # See usage overview
```

## Registry Format

`~/.agent-crap/registry.json`:

```json
{
  "version": 2,
  "projects": {
    "my-app": {
      "path": "/Users/bruno/Code/my-app",
      "registered": "2026-03-12T10:00:00+00:00",
      "last_sync": "2026-03-12T15:30:00+00:00",
      "tracked_files": [".claude/", "CLAUDE.md", ".cursor/"],
      "agents": ["claude-code", "cursor"],
      "tags": []
    }
  }
}
```

## Safety

- All destructive operations create automatic backups
- `unregister` restores files to their original locations
- `snapshot-restore` backs up current state before restoring
- `import` backs up existing store before overwriting
- Symlink verification before any destructive operation
- Registry is the single source of truth for mappings
- `doctor` catches corruption, orphans, and broken links
- `gc` only removes backups older than 30 days

---
> Source: [brunogama/ai-crap-manager](https://github.com/brunogama/ai-crap-manager) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
