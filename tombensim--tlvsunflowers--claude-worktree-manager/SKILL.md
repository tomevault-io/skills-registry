---
name: claude-worktree-manager
description: Create and manage Claude-specific worktrees with automated setup and cleanup. Use this skill (via Skill tool or direct script) when asked to "create a worktree", "new worktree", "worktree for feature/staging", "setup isolated environment", or "cleanup old worktrees". Script path is auto-detected when using Skill tool. Handles smart naming, .env copying, background pnpm install, and automatic cleanup of stale worktrees. Use when this capability is needed.
metadata:
  author: tombensim
---

# Claude Worktree Manager

Automated worktree management for Claude Code development sessions with smart naming, auto-setup, and cleanup.

## How to Use This Skill

There are two ways to create worktrees:

### 1. Via Claude Code Skill (Recommended)

Use the `Skill` tool to invoke this skill - **the script path is detected automatically**:

```javascript
// Using Skill tool with args parameter (Recommended)
Skill(skill: 'claude-worktree-manager', args: 'create feature-name --model opus')
Skill(skill: 'claude-worktree-manager', args: 'create feature-name --isolated --model sonnet')

// All bash script flags work the same via args
Skill(skill: 'claude-worktree-manager', args: 'list')
Skill(skill: 'claude-worktree-manager', args: 'cleanup --days 14')
```

The skill handles all path resolution using `git rev-parse --show-toplevel`, so you only need to provide the worktree name and optional flags. All bash script flags (`--model`, `--isolated`, `--days`) work exactly the same when passed via the `args` parameter.

### 2. Via Direct Script Invocation

If you're running the script manually, provide the full path from any directory:

```bash
.claude/skills/claude-worktree-manager/scripts/worktree.sh create feature-name --model opus
```

Or use the full absolute path:

```bash
/path/to/repo/.claude/skills/claude-worktree-manager/scripts/worktree.sh create feature-name --model opus
```

## Invocation Method Comparison

To clarify the correct syntax for each invocation method:

| Task                     | Skill Tool                                                                                | Direct Script                                                                                       |
| ------------------------ | ----------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------- |
| Create standard worktree | `Skill(skill: 'claude-worktree-manager', args: 'create my-feature')`                      | `.claude/skills/claude-worktree-manager/scripts/worktree.sh create my-feature`                      |
| With model flag          | `Skill(skill: 'claude-worktree-manager', args: 'create my-feature --model opus')`         | `.claude/skills/claude-worktree-manager/scripts/worktree.sh create my-feature --model opus`         |
| With isolated database   | `Skill(skill: 'claude-worktree-manager', args: 'create schema-test --isolated')`          | `.claude/skills/claude-worktree-manager/scripts/worktree.sh create schema-test --isolated`          |
| Both flags combined      | `Skill(skill: 'claude-worktree-manager', args: 'create complex --isolated --model opus')` | `.claude/skills/claude-worktree-manager/scripts/worktree.sh create complex --isolated --model opus` |
| List worktrees           | `Skill(skill: 'claude-worktree-manager', args: 'list')`                                   | `.claude/skills/claude-worktree-manager/scripts/worktree.sh list`                                   |
| Cleanup old worktrees    | `Skill(skill: 'claude-worktree-manager', args: 'cleanup --days 7')`                       | `.claude/skills/claude-worktree-manager/scripts/worktree.sh cleanup --days 7`                       |

**Key Differences:**

- **Skill tool:** All commands and flags are passed as a single string via the `args` parameter
- **Direct script:** Commands and flags are bash arguments, requires absolute or relative path

## Verifying Model Configuration

After creating a worktree with `--model`, verify the configuration was set correctly:

```bash
# Navigate to your worktree
cd /path/to/worktree

# Run verification script
.claude/skills/claude-worktree-manager/scripts/verify-worktree-model.sh

# Or verify from anywhere by passing the path
.claude/skills/claude-worktree-manager/scripts/verify-worktree-model.sh /path/to/worktree
```

**Expected output when successful:**

```
[INFO] Verifying Claude model configuration in: /path/to/worktree
[SUCCESS] .claude/settings.local.json found
[SUCCESS] Model configuration found: opus
[SUCCESS] Model is valid: opus
```

The validation script will:

1. Check that `.claude/settings.local.json` exists
2. Extract the model value using jq or grep
3. Validate the model is one of: opus, sonnet, haiku
4. Provide instructions if configuration is missing or invalid

See the **Model Flag Configuration - Edge Cases & Troubleshooting** section below for fixing common issues.

## Critical: Model Configuration Warning

**Before starting Claude in a worktree with `--model` flag, you MUST run the verification script.**

The model configuration uses `jq` (preferred) or `sed` (fallback) to update JSON files. These tools can **fail silently** on some systems, resulting in Claude using the wrong model.

### Quick Reference: Model Configuration Troubleshooting

| Symptom                                | Likely Cause         | Quick Fix                                                           |
| -------------------------------------- | -------------------- | ------------------------------------------------------------------- |
| Claude uses wrong model                | Verification skipped | Run `verify-worktree-model.sh` before starting Claude               |
| `[WARN] jq not found` in output        | jq not installed     | Install jq: `brew install jq` (macOS) or `apt install jq` (Linux)   |
| Model key missing in JSON              | sed fallback failed  | Manually add `"model": "opus"` to settings.local.json               |
| JSON file is corrupted                 | sed pattern mismatch | Delete settings.local.json, copy from main repo, add model manually |
| `[SUCCESS]` shown but wrong model used | Multiple model keys  | Check for duplicate `"model"` entries in JSON                       |

### Default Model Configuration

The script has a **default model** configured: `sonnet`

- All new worktrees automatically use this model unless overridden with `--model`
- To change the default, edit `DEFAULT_MODEL` in the script
- Override for specific worktrees: `--model opus` or `--model haiku`

**Important: Setting DEFAULT_MODEL does NOT make verification optional!**

Even with a default model configured, you MUST still run `verify-worktree-model.sh` after creating a worktree. The default model goes through the same jq/sed configuration process that can fail silently.

### Complete Workflow: Create, Verify, Start

**Scenario 1: Using default model (sonnet)**

```bash
# 1. Create worktree (automatically uses DEFAULT_MODEL=sonnet)
WORKTREE_PATH=$(.claude/skills/claude-worktree-manager/scripts/worktree.sh create my-feature | tail -1)
# Output: [INFO] Using default model: sonnet

# 2. REQUIRED: Verify the default model was set correctly
.claude/skills/claude-worktree-manager/scripts/verify-worktree-model.sh "$WORKTREE_PATH"
# Expected: [SUCCESS] Model is valid: sonnet

# 3. Only start Claude AFTER verification succeeds
cd "$WORKTREE_PATH" && claude 'Your goal'
```

**Scenario 2: Using custom model (opus)**

```bash
# 1. Create worktree with explicit model (overrides DEFAULT_MODEL)
WORKTREE_PATH=$(.claude/skills/claude-worktree-manager/scripts/worktree.sh create complex-task --model opus | tail -1)
# Output: [INFO] Setting default model to: opus

# 2. REQUIRED: Verify the custom model was set correctly
.claude/skills/claude-worktree-manager/scripts/verify-worktree-model.sh "$WORKTREE_PATH"
# Expected: [SUCCESS] Model is valid: opus

# 3. Only start Claude AFTER verification succeeds
cd "$WORKTREE_PATH" && claude 'Your complex goal'
```

## Quick Start

### Create a Worktree

When the user asks to create a worktree for a feature or environment, derive a short, descriptive kebab-case name from their request:

**Naming examples:**

- "staging environment" -> `staging-env`
- "add dark mode toggle" -> `dark-mode`
- "fix authentication bug" -> `fix-auth-bug`
- "update API endpoints" -> `update-api`

Then run:

```bash
# Standard (uses shared dev database)
.claude/skills/claude-worktree-manager/scripts/worktree.sh create <derived-name>

# With specific model (e.g., opus, sonnet, haiku)
.claude/skills/claude-worktree-manager/scripts/worktree.sh create <derived-name> --model opus

# Isolated (creates dedicated PostgreSQL database with migrations)
.claude/skills/claude-worktree-manager/scripts/worktree.sh create <derived-name> --isolated

# Isolated with model
.claude/skills/claude-worktree-manager/scripts/worktree.sh create <derived-name> --isolated --model sonnet
```

**What happens:**

1. Cleans up worktrees older than 7 days
2. Fetches latest from origin
3. Checks if a branch with your name already exists on origin:
   - **If found:** Creates local branch tracking the remote, pulls latest changes
   - **If not found:** Creates new branch: `worktree/<name>-<timestamp>` from main
4. Creates worktree at: `~/claude-worktrees/tennis-team/<name>-<timestamp>`
5. Copies `.env` from main repo
6. Copies `.claude/settings.local.json` from main repo (API keys, preferences)
7. Starts `pnpm install` in background
8. If `--model`: Sets default Claude model in `.claude/settings.local.json`
9. If `--isolated`: Creates dedicated PostgreSQL database and runs migrations
10. Returns the worktree path immediately

### Required: Verify Model Configuration Before Starting Claude

When creating worktrees with the `--model` flag, **you MUST verify the configuration before starting Claude**:

```bash
# 1. Create worktree with model
WORKTREE_PATH=$(.claude/skills/claude-worktree-manager/scripts/worktree.sh create my-feature --model opus | tail -1)

# 2. REQUIRED: Verify the model was set correctly
.claude/skills/claude-worktree-manager/scripts/verify-worktree-model.sh "$WORKTREE_PATH"
# Must show: [SUCCESS] Model is valid: opus

# 3. Check pnpm install progress (optional)
tail -f "$WORKTREE_PATH/.pnpm-install.log"

# 4. Only start Claude AFTER verification succeeds
cd "$WORKTREE_PATH"
claude 'Your goal here'
```

### When to Use --isolated

Use the `--isolated` flag when:

- **Schema changes:** Testing database migrations
- **Migration testing:** Verifying migration scripts work correctly
- **Isolated experiments:** Need a clean database state
- **Breaking changes:** Don't want to affect shared dev data

For normal feature development, skip `--isolated` to use the shared database.

### Model Selection

Use the `--model` flag to set the default Claude model for the worktree:

```bash
.claude/skills/claude-worktree-manager/scripts/worktree.sh create <name> --model <model-name>
```

**Available models:** `opus`, `sonnet`, `haiku`

When specified, the model is configured in the worktree's `.claude/settings.local.json` so that all Claude Code sessions in that worktree use the selected model by default.

### Ghostty Integration (Automatic)

The script automatically opens a new Ghostty tab when running in Ghostty terminal. Use the `--goal` flag to set a task description:

```bash
# Creates worktree and opens Ghostty tab with Claude
.claude/skills/claude-worktree-manager/scripts/worktree.sh create my-feature --goal "Add dark mode toggle"
```

The script will:

1. Create the worktree with all setup
2. Detect if running in Ghostty (`$TERM_PROGRAM = ghostty`)
3. Open a new tab using `ghostty-tab`
4. Navigate to the worktree directory
5. Type the Claude command (without executing, so you can review)

**Goal Prompt Guidelines:**

- Keep it concise (1-2 sentences max)
- Focus on the task objective
- Use imperative voice
- Avoid special characters that need escaping (use simple quotes)

### Script Configuration

The script has configurable variables at the top. **To customize, edit the script directly:**

| Variable               | Default                        | Description                                             |
| ---------------------- | ------------------------------ | ------------------------------------------------------- |
| `DEFAULT_MAX_AGE_DAYS` | `7`                            | Days before worktrees are considered stale for cleanup  |
| `WORKTREE_BASE`        | `$HOME/claude-worktrees`       | Base directory for all worktrees                        |
| `DEFAULT_MODEL`        | `sonnet`                       | Model saved to worktree's `.claude/settings.local.json` |
| `CLAUDE_CMD`           | `cc`                           | Command to run Claude (use your shell alias)            |
| `GHOSTTY_TAB`          | `$HOME/.local/bin/ghostty-tab` | Path to ghostty-tab binary                              |

### List Worktrees

```bash
.claude/skills/claude-worktree-manager/scripts/worktree.sh list
```

Shows all active worktrees for the current project with their branches.

### Manual Cleanup

```bash
# Cleanup worktrees older than 7 days (default)
.claude/skills/claude-worktree-manager/scripts/worktree.sh cleanup

# Custom age threshold
.claude/skills/claude-worktree-manager/scripts/worktree.sh cleanup --days 14
```

## Origin Branch Handling

The script intelligently checks if a branch with your name already exists on origin:

**Scenario 1: Branch exists on origin**

If `origin/my-feature` exists, the script will:

- Create a local branch tracking `origin/my-feature`
- Pull the latest changes automatically
- Create the worktree on this branch
- Useful for continuing work on an existing feature branch

**Scenario 2: Branch doesn't exist on origin**

If `origin/my-feature` doesn't exist, the script will:

- Create a new branch from main: `worktree/my-feature-<timestamp>`
- Create a fresh worktree for new development

## Background Installation

The worktree creation starts `pnpm install` in the background using `nohup`. This means:

- The path is returned immediately (don't wait for pnpm)
- Installation runs async and logs to `.pnpm-install.log`
- User can start working right away
- Dependencies will be available after a few moments

Check if installation is complete:

```bash
# Check if still running
ps aux | grep pnpm

# Watch the log
tail -f <worktree-path>/.pnpm-install.log
```

## Database Setup (Isolated Mode)

When using `--isolated`, the script automatically:

1. Checks if Docker PostgreSQL container is running (starts it if needed via `pnpm docker:infra`)
2. Creates a new PostgreSQL database: `tennis_worktree_<timestamp>`
3. Updates the worktree's `.env` with the new `DATABASE_URL`
4. Runs database migrations via `pnpm --filter @tennis/backend run db:migrate`

Database setup starts after pnpm install completes. Check progress:

```bash
tail -f <worktree-path>/.db-setup.log
```

### Isolated DB Cleanup

When you're done with an isolated worktree, clean up its database:

```bash
# List worktree databases
psql -h localhost -U postgres -c "SELECT datname FROM pg_database WHERE datname LIKE 'tennis_worktree_%';"

# Drop a specific worktree database
psql -h localhost -U postgres -c "DROP DATABASE tennis_worktree_<timestamp>;"
```

## Integration with worktree-operations

After creating the worktree, users can reference the **worktree-operations** skill for:

- Docker infrastructure: `pnpm docker:infra`
- Database migrations: `pnpm --filter @tennis/backend run db:migrate`
- Building packages: `pnpm run build`
- Running tests: `pnpm test`
- Development mode: `pnpm run dev`
- Type checking: `pnpm run typecheck`

The worktree-operations skill covers all pnpm/turbo commands for working in the monorepo.

## Configuration Files

The script automatically handles configuration files:

**Automatically Available (via git):**

- `.claude/skills/` - All skills are available in worktrees
- `.claude/settings.json` - Committed Claude settings

**Automatically Copied:**

- `.env` - Environment variables for the application
- `.claude/settings.local.json` - Local Claude settings (API keys, preferences)

This ensures your worktree has the same development environment as the main repo.

## Directory Structure

```
~/claude-worktrees/
└── tennis-team/
    ├── feature-a-1736639420/
    ├── staging-env-1736639421/
    └── fix-bug-1736639422/
```

Each project gets its own subdirectory under `~/claude-worktrees/`.

## Cleanup Policy

- **Automatic:** Runs before each worktree creation
- **Threshold:** Removes worktrees older than 7 days (based on modification time)
- **Safe:** Only removes worktrees in the Claude worktree directory
- **Manual:** Can be triggered anytime with `./worktree.sh cleanup`

## .env Configuration Requirements

The `.env` file is copied to worktrees automatically. Ensure proper formatting to avoid shell parsing errors:

### Quote Special Characters

Values with special characters MUST be quoted:

```bash
# Correct - quoted values
DATABASE_URL="postgresql://postgres:postgres@localhost:5432/tennis_dev"
REDIS_URL="redis://localhost:6379"

# Wrong - unquoted special chars cause shell errors
DATABASE_URL=postgresql://postgres:p@ss@localhost:5432/tennis_dev
```

### Required Variables

For the application to work, ensure these are set:

```bash
DATABASE_URL="postgresql://postgres:postgres@localhost:5432/tennis_dev"
REDIS_URL="redis://localhost:6379"
```

## Model Flag Configuration - Edge Cases & Troubleshooting

The `--model` flag updates `.claude/settings.local.json` to set the default Claude model for your worktree. This section documents potential edge cases and how to handle them.

### Requirements

The script requires one of the following tools to update JSON:

1. **jq** (preferred) - Safely parses and updates JSON
2. **sed** (fallback) - Uses text substitution, less reliable but works when jq is unavailable

Most systems have `sed` built-in. To install `jq`:

```bash
# macOS
brew install jq

# Ubuntu/Debian
sudo apt-get install jq
```

### Troubleshooting: Model Not Applied

If Claude doesn't use the configured model:

**Step 1: Verify the model was set**

```bash
cd /path/to/worktree
.claude/skills/claude-worktree-manager/scripts/verify-worktree-model.sh
```

**Step 2: Check settings.local.json contents**

```bash
cat /path/to/worktree/.claude/settings.local.json | grep -i model
```

**Step 3: If model is missing, add it manually**

Edit `.claude/settings.local.json` and add `"model": "opus"` at the top level.

### Manual Model Configuration

If automatic configuration fails, manually edit `.claude/settings.local.json`:

```json
{
  "model": "opus",
  "permissions": {
    "allow": [ ... ]
  }
}
```

**Valid model values:**

- `"opus"` - Claude Opus 4.5 (most capable)
- `"sonnet"` - Claude Sonnet (balanced)
- `"haiku"` - Claude Haiku (fastest/cheapest)

## Troubleshooting

### pnpm install failed

Check the log:

```bash
cat <worktree-path>/.pnpm-install.log
```

Re-run manually if needed:

```bash
cd <worktree-path>
pnpm install
```

### .env not copied

Copy manually:

```bash
cp <main-repo>/.env <worktree-path>/.env
```

### Database setup failed

Check the log:

```bash
cat <worktree-path>/.db-setup.log
```

Common issues:

- Docker containers not running: Run `pnpm docker:infra` from the main repo
- PostgreSQL connection refused: Check Docker is running with `docker ps`
- Missing tables: Run `pnpm --filter @tennis/backend run db:migrate`

### Worktree creation failed

Check git status and ensure:

- You're in a git repository
- Remote is accessible
- No uncommitted changes blocking the operation

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/tombensim) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
