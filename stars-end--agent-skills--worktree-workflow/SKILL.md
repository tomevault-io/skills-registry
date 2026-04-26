---
name: worktree-workflow
description: | Use when this capability is needed.
metadata:
  author: stars-end
---

# Worktree Workflow (Workspace-First V8.6)

## Goal

Keep canonical clones clean mirrors and put all writable work in isolated workspaces:

- **Canonical repos**: `~/{agent-skills,prime-radiant-ai,affordabot,llm-common}` (read-only for agents)
- **Workspaces**: `/tmp/agents/<beads-id>/<repo>` (all mutating work happens here)

## Workspace-First Contract (DX V8.6)

### 1. Create Workspace (Recommended Default)

```bash
dx-worktree create <beads-id> <repo>
# Prints workspace path: /tmp/agents/<beads-id>/<repo>
```

For Railway-linked repos, this also seeds `.dx/railway-context.env` and attempts a non-interactive `railway link` in the worktree.

### 2. Open/Resume Workspace (Manual IDE Sessions)

```bash
# Path-only mode (prints status)
dx-worktree open <beads-id> <repo>
dx-worktree resume <beads-id> <repo>

# Command execution mode
dx-worktree open <beads-id> <repo> -- opencode
dx-worktree open <beads-id> <repo> -- antigravity
dx-worktree open <beads-id> <repo> -- codex
dx-worktree open <beads-id> <repo> -- claude
dx-worktree open <beads-id> <repo> -- gemini
```

### 3. Recover Dirty Canonical Repo

```bash
dx-worktree evacuate-canonical <repo>
# Creates recovery worktree: /tmp/agents/recovery-<timestamp>/<repo>
# Resets canonical to clean state
```

**Skip conditions** (no destructive recovery):
- `.git/index.lock` present
- merge/rebase in progress
- active session lock
- repo already clean

### 4. Cleanup Workspace

```bash
dx-worktree cleanup <beads-id>
dx-worktree prune <repo>
```

## Guidance for Agents (Simple Rules)

- **Never edit in canonical repos** (`~/{agent-skills,prime-radiant-ai,affordabot,llm-common}`)
- **Always work inside workspace paths** (`/tmp/agents/<beads-id>/<repo>`)
- **Use `bdx` for Beads coordination commands** (`create`, `show`, `ready`, `comments`, `note`, etc.)
- **Use raw `bd` only** for local diagnostics/bootstrap/path-sensitive operations or explicit override
- **Recovery uses named worktrees**, not stash
- If stuck: `dx-worktree evacuate-canonical <repo>` → `dx-worktree create <beads-id> <repo>`

## DX V8.6 Governance

### dx-runner and dx-batch Enforcement

Both tools **reject** canonical repo paths for mutating execution with:

```
reason_code=canonical_worktree_forbidden
rejected_path=~/agent-skills
remedy=dx-worktree create <beads-id> <repo>
```

### Normal Operations Still Work

These operations are **unaffected** in canonical repos:
- `git fetch`, `git checkout master`, `git pull --ff-only`
- `railway status`, `railway run`, `railway shell`
- Normal shell startup
- Loading skills from `~/agent-skills`

**Blocked**: Mutating agent execution against canonical roots

## Keep Your Work Safe

> **Policy (DX V8.6):** Uncommitted work older than 48h is considered stale and will be GC'd. Commit your work at logical milestones.

### Rules

- **Open a draft PR after your first real commit** — makes work visible
- **Commit at logical milestones** — not on a timer
- **Uncommitted changes are your responsibility** — no cron will save them

## Recovery Branch Naming

When evacuating canonical repos:
```
recovery/canonical-<repo>-<timestamp>
```

Example:
```
recovery/canonical-agent-skills-20260306T101530Z
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/stars-end) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
