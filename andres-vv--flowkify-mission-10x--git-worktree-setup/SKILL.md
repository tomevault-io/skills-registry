---
name: git-worktree-setup
description: Create isolated git worktrees for parallel feature development. Use when the user asks to implement, build, or work on a feature "in parallel", "on the side", "separately", "without affecting current work", or "in a new branch". Also triggers on "start a new ticket", "create a worktree", "set up isolated development", or working on multiple things simultaneously. If the user says "in parallel" about any feature or ticket, ALWAYS use this skill. Use when this capability is needed.
metadata:
  author: andres-vv
---

<!-- ================================================================
     PROJECT CONFIGURATION — update these values for your project
     ================================================================ -->

## Configuration

| Variable | Value |
|----------|-------|
| `BASE_BRANCH` | |

<!-- ================================================================ -->

# Git Worktree Setup

When the user asks to work on something **in parallel** or **separately**, always create a git worktree. This ensures the new work is fully isolated in its own directory and Cursor window, without affecting any in-progress work.

## Worktree location

All worktrees live in `../worktrees/` relative to the repository root.

## Workflow

### 1. Choose branch prefix

Every branch **must** use one of these prefixes:

| Prefix   | Use when |
|----------|----------|
| `feature` | New functionality, new screens, new flows. Default when unclear. |
| `bugfix`  | Fixing a bug, crash, or incorrect behaviour. |
| `qol`     | Quality of life: UI polish, small improvements, refactors, DX. |

Derive from the user's request (e.g. "fix the login crash" → bugfix, "tweak the dashboard" → qol).

### 2. Determine ticket context

- **Ticket number provided** (e.g. "ticket 1234", "AB#1087"): use it. The branch name **will include the ticket** (e.g. `feature/AB-1234-short-description`).
- **No ticket, just a description**: use `--no-ticket`. Branch has no ticket number (e.g. `feature/short-description`).
- **Ambiguous**: ask if they want to link an ADO ticket, or proceed with `--no-ticket`.

### 3. Run the setup script

Execute from the repository root. **First argument is always the prefix** (`feature`, `bugfix`, or `qol`). The script copies `.env` from the repo root into the worktree so the new worktree has the same environment variables.

**With a ticket number** (branch includes ticket):

```bash
bash scripts/worktree/new-worktree.sh <prefix> <ticket-number> <short-description>
```

Examples:
- `bash scripts/worktree/new-worktree.sh feature 1234 export-feature` → `feature/AB-1234-export-feature`
- `bash scripts/worktree/new-worktree.sh bugfix 5678 crash-fix` → `bugfix/AB-5678-crash-fix`
- `bash scripts/worktree/new-worktree.sh qol 1087 ui-tweaks` → `qol/AB-1087-ui-tweaks`

**Without a ticket number:**

```bash
bash scripts/worktree/new-worktree.sh <prefix> --no-ticket <short-description>
```

Examples:
- `bash scripts/worktree/new-worktree.sh feature --no-ticket login-page-redesign` → `feature/login-page-redesign`
- `bash scripts/worktree/new-worktree.sh bugfix --no-ticket pdf-crash` → `bugfix/pdf-crash`

**Short description:** lowercase, kebab-case, 2–4 words. Examples: `export-feature`, `login-page-redesign`, `crash-fix`.

The script will:

1. Fetch latest from remote (`git fetch origin`)
2. Resolve the latest `origin/{BASE_BRANCH}` SHA and verify no stale local branch exists
3. Create a **new branch** starting from the latest `origin/{BASE_BRANCH}` — always a fresh checkout, never a local or outdated `{BASE_BRANCH}`
4. Copy `.env` into the worktree
5. Copy `.cursor/mcp.json` into the worktree (if present), so MCP config is available in the new worktree
6. Run `npm install`
7. Run `npm run prisma generate` (if Prisma is used: `prisma/schema.prisma` or `prisma/` dir exists)
8. Open the worktree in a new Cursor window via the `cursor` CLI

### 4. Confirm to the user

Report:

- The worktree path
- The branch name (with prefix: feature/, bugfix/, or qol/; with ticket number when one was used)
- That a new Cursor window has been opened
- Remind them they can start working in the new window

### 5. (Optional) Set ADO ticket to Active

Only if a ticket number was provided or resolved. Use **user-ado** MCP:

```
wit_update_work_item(id, updates: [{ op: "add", path: "/fields/System.State", value: "Active" }])
```

## Listing existing worktrees

```bash
git worktree list
```

## Important notes

- Never check out the same branch in two worktrees.
- Each worktree has its own `node_modules`, `.env`, and `.cursor/mcp.json` (copied at setup) — they are not shared.
- The `.cursor/` directory (rules, skills, agents) is part of the repo and available in every worktree automatically; `.cursor/mcp.json` is copied from the repo where you run the script so the new worktree gets the same MCP config.
- If the worktree directory already exists, the script will abort — remove the old one first or choose a different name.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/andres-vv) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
