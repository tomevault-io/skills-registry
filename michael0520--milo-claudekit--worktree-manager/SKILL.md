---
name: worktree-manager
description: >- Use when this capability is needed.
metadata:
  author: michael0520
---

Manage parallel development across all projects using git worktrees with Claude Code agents. Each worktree is an isolated repo copy on a different branch, stored centrally at `~/tmp/worktrees/`.

All operations can be performed manually with `jq`, `git`, and `bash`. Scripts are helpers, not requirements -- fall back to manual steps if a script fails.

## File Locations

| File | Purpose |
|------|---------|
| `~/.claude/worktree-registry.json` | Global registry -- tracks all worktrees across all projects |
| `~/.claude/skills/worktree-manager/config.json` | Skill config -- terminal, shell, port range settings |
| `~/.claude/skills/worktree-manager/scripts/` | Helper scripts -- optional |
| `~/tmp/worktrees/` | Worktree storage -- all worktrees live here |
| `.claude/worktree.json` (per-project) | Project config -- optional custom settings |

## Core Concepts

### Centralized Storage

All worktrees live in `~/tmp/worktrees/<project-name>/<branch-slug>/`:

```
~/tmp/worktrees/
├── obsidian-ai-agent/
│   ├── feature-auth/           # branch: feature/auth
│   ├── feature-payments/       # branch: feature/payments
│   └── fix-login-bug/          # branch: fix/login-bug
└── another-project/
    └── feature-dark-mode/
```

### Branch Slug Convention

Replace `/` with `-` for filesystem safety:

```bash
echo "feature/auth" | tr '/' '-'   # feature-auth
```

### Port Allocation

- **Global pool**: 8100-8199 (100 ports, 2 per worktree)
- Ports are tracked globally to avoid conflicts across projects
- Always verify availability: `lsof -i :<port>`

## References

| Topic | Description | Reference |
|-------|-------------|-----------|
| Registry | Global registry schema, fields, and manual operations | [registry](references/registry.md) |
| Workflows | Create worktrees, launch agents, check status | [workflows](references/workflows.md) |
| Cleanup | Cleanup procedures and safety guidelines | [cleanup](references/cleanup.md) |
| Detection | Package manager detection, dev server detection, port allocation | [detection](references/detection.md) |
| Sparse-Checkout | Sparse-checkout setup and configuration | [sparse-checkout](references/sparse-checkout.md) |

## Script Reference

Scripts are in `~/.claude/skills/worktree-manager/scripts/`.

| Script | Usage | Description |
|--------|-------|-------------|
| `allocate-ports.sh` | `allocate-ports.sh <count>` | Returns space-separated ports, updates registry |
| `sparse-checkout.sh` | `sparse-checkout.sh setup\|list\|add\|disable <path> [dirs...]` | Manage sparse-checkout for a worktree |
| `register.sh` | `register.sh <project> <branch> <slug> <path> <repo> <ports> [task] [sparse-enabled] [sparse-dirs]` | Register worktree in global registry |
| `launch-agent.sh` | `launch-agent.sh <path> [task]` | Open new terminal window with Claude Code |
| `status.sh` | `status.sh [--project <name>]` | Show all worktrees or filter by project |
| `cleanup.sh` | `cleanup.sh <project> <branch> [--delete-branch]` | Kill ports, remove worktree, update registry |
| `release-ports.sh` | `release-ports.sh <port1> [port2] ...` | Release ports back to pool |

## Skill Config

Location: `~/.claude/skills/worktree-manager/config.json`

```json
{
  "terminal": "ghostty",
  "shell": "bash",
  "claudeCommand": "claude --dangerously-skip-permissions",
  "portPool": { "start": 8100, "end": 8199 },
  "portsPerWorktree": 2,
  "worktreeBase": "~/tmp/worktrees",
  "defaultCopyDirs": [".agents", ".env.example"],
  "sparseCheckout": { "enabled": false, "defaultDirectories": [] }
}
```

- **terminal**: `ghostty`, `iterm2`, `tmux`, `wezterm`, `kitty`, `alacritty`
- **shell**: `bash`, `zsh`, `fish`
- **claudeCommand**: Command to launch Claude Code

## Project Config (Optional)

Place `.claude/worktree.json` in any project for custom settings:

```json
{
  "ports": { "count": 2, "services": ["api", "frontend"] },
  "install": "uv sync && cd frontend && npm install",
  "validate": {
    "start": "docker-compose up -d",
    "healthCheck": "curl -sf http://localhost:{{PORT}}/health",
    "stop": "docker-compose down"
  },
  "copyDirs": [".agents", ".env.example", "data/fixtures"],
  "sparseCheckout": {
    "enabled": true,
    "directories": ["apps/api", "apps/frontend", "packages/core", "packages/shared"]
  }
}
```

Use these settings when present. Otherwise, auto-detect.

## Common Issues

| Problem | Fix |
|---------|-----|
| Worktree already exists | `git worktree list` then `git worktree remove <path> --force && git worktree prune` |
| Branch already exists | Omit `-b` flag: `git worktree add <path> <branch>` |
| Port already in use | `lsof -i :<port>` -- kill if stale, or pick a different port |
| Registry out of sync | Compare `jq '.worktrees[].worktreePath' ~/.claude/worktree-registry.json` with `find ~/tmp/worktrees -maxdepth 2 -type d` |
| Validation failed | Check stderr/logs, common causes: missing env vars, database not running, wrong port |
| Sparse-checkout shows unexpected files | Re-init: `git sparse-checkout init --cone && git sparse-checkout set <dirs>` |
| Sparse-checkout disabled accidentally | Re-enable with dirs from registry: `jq -r '.worktrees[] \| select(.worktreePath == "...") \| .sparseCheckout.directories[]'` |
| Need a directory not in sparse-checkout | `sparse-checkout.sh add $WORKTREE_PATH apps/new-dir` or manually append to `git sparse-checkout set` |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/michael0520) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
