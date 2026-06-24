---
name: worktree
description: Create an isolated git worktree for code changes. Use when starting work on a new feature, bug fix, or any changes that should be on a separate branch. Keeps the main working directory clean. Use when this capability is needed.
metadata:
  author: shawnrushefsky
---

# Git Worktree Workflow

Create and manage isolated git worktrees for parallel development with automatic permission inheritance.

## Permission Handling

Claude Code permissions work as follows in worktrees:

| Settings File | Location | Behavior in Worktrees |
|--------------|----------|----------------------|
| `.claude/settings.json` | Project (committed) | **Automatically inherited** - shared via git |
| `.claude/settings.local.json` | Project (gitignored) | **Not inherited** - must be copied manually |
| `~/.claude/settings.json` | User global | Always available in all worktrees |

**Key insight**: If your team's permissions are in `.claude/settings.json` (committed), all worktrees automatically get them with zero additional setup.

## Creating a Worktree

### Step 1: Gather Information

If not already clear, ask:
- **Feature name**: What are you working on? (will be used for branch/directory name)
- **Base branch**: Create from main/master or another branch?

### Step 2: Verify Prerequisites

```bash
# Ensure we're in a git repository
git rev-parse --show-toplevel || { echo "Not a git repository"; exit 1; }

# Check for uncommitted changes that might cause issues
git status --porcelain
```

### Step 3: Create the Worktree

```bash
# Set up variables
REPO_ROOT=$(git rev-parse --show-toplevel)
REPO_NAME=$(basename "$REPO_ROOT")
PARENT_DIR=$(dirname "$REPO_ROOT")
FEATURE_NAME="<kebab-case-feature-name>"  # e.g., add-auth, fix-pagination
BASE_BRANCH="${BASE_BRANCH:-main}"  # or master, develop, etc.

# Create worktree with new branch
WORKTREE_PATH="$PARENT_DIR/$REPO_NAME-$FEATURE_NAME"
git worktree add "$WORKTREE_PATH" -b "$FEATURE_NAME" "$BASE_BRANCH"
```

### Step 4: Copy Local Settings (if they exist)

```bash
# Copy local settings if present (these are gitignored so not shared automatically)
if [ -f "$REPO_ROOT/.claude/settings.local.json" ]; then
    mkdir -p "$WORKTREE_PATH/.claude"
    cp "$REPO_ROOT/.claude/settings.local.json" "$WORKTREE_PATH/.claude/"
    echo "Copied .claude/settings.local.json to worktree"
fi
```

### Step 5: Initialize Environment

Detect and run appropriate setup commands:

```bash
cd "$WORKTREE_PATH"

# Node.js projects
if [ -f "package-lock.json" ]; then
    npm ci  # or npm install
elif [ -f "yarn.lock" ]; then
    yarn install --frozen-lockfile
elif [ -f "pnpm-lock.yaml" ]; then
    pnpm install --frozen-lockfile
elif [ -f "bun.lockb" ]; then
    bun install

# Python projects
elif [ -f "requirements.txt" ]; then
    # Suggest virtual environment
    echo "Python project detected. Consider:"
    echo "  python -m venv .venv && source .venv/bin/activate && pip install -r requirements.txt"
elif [ -f "pyproject.toml" ]; then
    # Poetry or other Python build system
    if command -v poetry &> /dev/null && grep -q "\[tool.poetry\]" pyproject.toml; then
        poetry install
    elif command -v uv &> /dev/null; then
        uv sync
    fi

# Go projects
elif [ -f "go.mod" ]; then
    go mod download

# Rust projects
elif [ -f "Cargo.toml" ]; then
    cargo fetch
fi
```

### Step 6: Confirm Setup

```bash
echo ""
echo "Worktree created successfully!"
echo "  Directory: $WORKTREE_PATH"
echo "  Branch: $FEATURE_NAME"
echo "  Based on: $BASE_BRANCH"
echo ""
echo "To start working:"
echo "  cd $WORKTREE_PATH"
echo "  claude"
```

## Managing Worktrees

### List All Worktrees

```bash
git worktree list
```

### Switch to an Existing Worktree

```bash
# Just cd to the worktree directory
cd "$PARENT_DIR/$REPO_NAME-<feature-name>"
```

### Check Worktree Status

```bash
# From any worktree, list all with status
git worktree list --porcelain
```

## Completing Work

### After Making Changes

1. **Commit your changes**:
   ```bash
   git add -A
   git commit -m "feat: description of changes"
   ```

2. **Push the branch**:
   ```bash
   git push -u origin "$FEATURE_NAME"
   ```

3. **Create a PR** (optional):
   ```bash
   gh pr create --fill
   ```

### Cleanup

```bash
# Return to main repository
cd "$PARENT_DIR/$REPO_NAME"

# Remove the worktree (use -f if there are uncommitted changes)
git worktree remove "$WORKTREE_PATH"

# If branch was merged, delete it
git branch -d "$FEATURE_NAME"
```

### Bulk Cleanup (remove stale worktrees)

```bash
# Preview what would be pruned
git worktree prune --dry-run

# Actually prune stale worktree entries
git worktree prune
```

## Error Handling

### Worktree Already Exists

```bash
# Check if worktree exists
if git worktree list | grep -q "$FEATURE_NAME"; then
    echo "Worktree for '$FEATURE_NAME' already exists."
    # Offer: reuse existing, create with suffix, or abort
fi
```

### Branch Already Exists

```bash
# If branch exists but no worktree, use it
git worktree add "$WORKTREE_PATH" "$FEATURE_NAME"

# Or create with a unique suffix
git worktree add "$WORKTREE_PATH-2" -b "$FEATURE_NAME-2" "$BASE_BRANCH"
```

### Dirty Worktree During Removal

```bash
# Force remove (discards uncommitted changes!)
git worktree remove --force "$WORKTREE_PATH"
```

## Best Practices

1. **Permission Setup**: Put common tool permissions in `.claude/settings.json` (committed) so all worktrees inherit them automatically

2. **Naming Convention**: Use descriptive names like `repo-feature-auth`, `repo-fix-login-bug`

3. **Environment Variables**: Keep `.env` files consistent or copy them to new worktrees

4. **Session Isolation**: Each worktree gets its own Claude Code session context - use `/resume` to see sessions from all worktrees in the same repo

5. **Cleanup Regularly**: Remove worktrees after PRs are merged to avoid confusion

## Quick Reference

| Task | Command |
|------|---------|
| Create worktree | `git worktree add ../repo-feature -b feature main` |
| List worktrees | `git worktree list` |
| Remove worktree | `git worktree remove ../repo-feature` |
| Prune stale | `git worktree prune` |
| Lock worktree | `git worktree lock ../repo-feature` |
| Unlock worktree | `git worktree unlock ../repo-feature` |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/shawnrushefsky) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
