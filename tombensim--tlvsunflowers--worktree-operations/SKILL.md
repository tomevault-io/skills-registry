---
name: worktree-operations
description: Guide for working within git worktrees in the tennis-team monorepo. Use this skill when you detect you're working in a worktree (path contains "claude-worktrees" or is outside the main project directory), when asked to "set up worktree", "install dependencies in worktree", "build in worktree", "merge worktree", or when troubleshooting worktree-specific issues. Covers Docker infrastructure, PostgreSQL/Redis setup, pnpm/turbo commands, environment setup, git workflows (merging, conflict resolution, cleanup), and common worktree pitfalls. Use when this capability is needed.
metadata:
  author: tombensim
---

# Worktree Operations

## Detecting a Worktree

You're in a worktree if:

- Path contains `claude-worktrees` (e.g., `~/claude-worktrees/tennis-team/my-feature-123456`)
- Path is outside the main project directory but contains project files
- Branch name starts with `worktree/`

Worktrees are isolated checkouts used for parallel development without affecting the main checkout.

## Initial Setup (After Worktree Creation)

When a worktree is first created, run these commands:

```bash
# Install dependencies with pnpm (runs automatically via worktree creation script)
pnpm install

# Start Docker infrastructure (PostgreSQL + Redis)
pnpm docker:infra

# Run database migrations
pnpm --filter @tennis/backend run db:migrate
```

## Tennis-Team Project Structure

```
tennis-team/
├── apps/
│   ├── backend/          # Express API server (@tennis/backend)
│   ├── frontend/         # React frontend app (@tennis/frontend)
│   └── website/          # Public website (@tennis/website)
├── packages/
│   └── shared/           # Shared types and utilities (@tennis/shared)
├── docker/
│   ├── docker-compose.yml        # Infrastructure services
│   └── docker-compose.dev.yml    # Dev overrides
├── pnpm-workspace.yaml
├── turbo.json
└── package.json
```

### Referencing Packages

Use workspace protocol in `package.json`:

```json
{
  "dependencies": {
    "@tennis/shared": "workspace:*"
  }
}
```

## Docker Infrastructure Setup

The tennis-team project uses Docker for PostgreSQL and Redis.

### Starting Infrastructure

```bash
# Start PostgreSQL + Redis (from project root)
pnpm docker:infra

# Start full dev environment (all services)
pnpm docker:dev

# Check container status
docker compose -f docker/docker-compose.yml ps
```

### Verifying Services

```bash
# Check PostgreSQL
psql -h localhost -U postgres -c "SELECT 1;"

# Check Redis
redis-cli ping
# Should return: PONG
```

### Troubleshooting Docker

```bash
# Containers not starting
docker compose -f docker/docker-compose.yml logs postgres
docker compose -f docker/docker-compose.yml logs redis

# Reset containers
docker compose -f docker/docker-compose.yml down -v
pnpm docker:infra
```

## Database Setup in Worktrees

Worktrees can use either a **shared database** (default) or an **isolated database** (for schema testing).

### Shared Database (Default)

Uses the same database as your main development environment. No additional setup needed.

```bash
# Your .env already points to the shared dev database
# Just ensure Docker is running and start developing!
pnpm docker:infra
pnpm run dev
```

### Isolated Database (For Schema Testing)

When you need a separate database (for migrations, schema changes, or isolated testing), use the `--isolated` flag when creating the worktree:

```bash
# Via worktree manager
.claude/skills/claude-worktree-manager/scripts/worktree.sh create my-feature --isolated
```

This automatically:

1. Creates a new PostgreSQL database: `tennis_worktree_<timestamp>`
2. Updates your `.env` with the new `DATABASE_URL`
3. Runs all migrations via `pnpm --filter @tennis/backend run db:migrate`

### Manual Database Operations

```bash
# Run migrations
pnpm --filter @tennis/backend run db:migrate

# Connect to database
psql -h localhost -U postgres -d tennis_dev

# List all databases
psql -h localhost -U postgres -c "\l"
```

### Isolated DB Cleanup

```bash
# List worktree databases
psql -h localhost -U postgres -c "SELECT datname FROM pg_database WHERE datname LIKE 'tennis_worktree_%';"

# Drop a specific worktree database
psql -h localhost -U postgres -c "DROP DATABASE tennis_worktree_<timestamp>;"
```

### When to Use Isolated Database

Use isolated database when:

- Testing new migrations before merging
- Developing schema changes
- Running destructive tests
- Need a clean database state

Skip isolated database when:

- Normal feature development
- Bug fixes
- Frontend work
- Changes that don't touch the database

## Redis Cache Management

Redis is used for caching. In worktrees:

```bash
# Check Redis connection
redis-cli ping

# Clear all cache (development only)
redis-cli FLUSHALL

# Monitor Redis activity
redis-cli MONITOR
```

Worktrees share the same Redis instance by default. If you need isolation, use a different Redis database number in your `.env`:

```bash
REDIS_URL="redis://localhost:6379/1"  # Use DB 1 instead of default 0
```

## Tennis-Team Dev Workflow

### Starting Development

```bash
# Start infrastructure first
pnpm docker:infra

# Start all apps in dev mode (backend + frontend + website)
pnpm dev

# Or start specific apps
pnpm --filter @tennis/backend run dev
pnpm --filter @tennis/frontend run dev
```

### Common Commands (pnpm + turbo)

#### Building

```bash
# Build all packages
pnpm run build

# Build a specific package
pnpm --filter @tennis/backend run build
pnpm --filter @tennis/shared run build

# Build with turbo (uses caching)
pnpm turbo run build

# Force rebuild (ignore cache)
pnpm turbo run build --force
```

#### Testing

```bash
# Run all tests
pnpm test

# Run tests for a specific package
pnpm --filter @tennis/backend run test

# Run with turbo (respects dependencies)
pnpm turbo run test
```

#### Development

```bash
# Start development mode (all apps)
pnpm run dev

# Run linting
pnpm run lint

# Type checking
pnpm run typecheck
```

## pnpm and Turbo in Monorepos

### pnpm Workspace Architecture

**How pnpm Works**:

- Content-addressable store at `~/.pnpm-store`
- Symlinked `node_modules` (not flat like npm)
- Shared dependencies across all worktrees
- Hard links from store to project `node_modules`

**Directory Structure**:

```
project/
├── node_modules/
│   ├── .pnpm/                    # Actual packages (symlinked)
│   ├── @tennis/shared -> .pnpm/...  # Workspace packages
│   └── react -> .pnpm/...        # External packages
├── apps/
│   ├── backend/
│   │   └── node_modules/ -> ../../node_modules
│   └── frontend/
│       └── node_modules/ -> ../../node_modules
└── pnpm-lock.yaml               # Lock file (CRITICAL)
```

### Dependency Installation Patterns

#### Pattern 1: Root Level Dependencies (Shared)

```bash
# Install dependency for all packages
pnpm add -w typescript  # -w = workspace root

# Install dev dependency at root
pnpm add -D -w eslint
```

#### Pattern 2: Package-Specific Dependencies

```bash
# Install in specific package
pnpm --filter @tennis/backend add express

# Install dev dependency in package
pnpm --filter @tennis/backend add -D jest
```

#### Pattern 3: Workspace Protocol Dependencies

```json
// apps/backend/package.json
{
  "dependencies": {
    "@tennis/shared": "workspace:*"
  }
}
```

### Lock File Management

#### Understanding pnpm-lock.yaml

**Critical File**: `pnpm-lock.yaml` must be committed and kept in sync.

**Lock File Rules**:

1. Commit `pnpm-lock.yaml` to git
2. Never manually edit lock file
3. Run `pnpm install` after pulling changes
4. Don't ignore lock file in `.gitignore`

#### Handling Lock File Conflicts

```bash
# During merge conflict - accept theirs and regenerate (RECOMMENDED)
git checkout --theirs pnpm-lock.yaml
pnpm install  # Regenerates lock file with your changes

# After resolving
git add pnpm-lock.yaml
git commit --no-edit
```

### Common pnpm Issues in Worktrees

#### Issue 1: .pnpm-install.pid Blocking Operations

```bash
# Before merging branches
rm -f .pnpm-install.pid

# If locked by process
lsof .pnpm-install.pid  # Find process using file
kill <PID>
rm .pnpm-install.pid
```

#### Issue 2: node_modules Symlink Confusion

```bash
# Workspace packages symlink
ls -la node_modules/@tennis/shared  # -> ../packages/shared
```

#### Issue 3: Workspace Dependencies Not Updating

```bash
# Rebuild affected packages
pnpm --filter @tennis/shared run build

# Or rebuild everything
pnpm turbo run build --force
```

#### Issue 4: Different pnpm Versions

```bash
# Check required version
cat package.json | jq .packageManager

# Install correct version globally
npm install -g pnpm@9.15.4

# Or use Corepack (recommended)
corepack enable
corepack prepare pnpm@9.15.4 --activate
```

### Turbo Build Caching

#### Turbo in Worktrees

**Issue**: Turbo cache is worktree-local

```bash
# Workaround: Share cache between worktrees
export TURBO_CACHE_DIR=~/.turbo-cache
```

#### Cache Invalidation

```bash
# Clear turbo cache
pnpm turbo run build --force  # Bypass cache

# Delete cache directory
rm -rf node_modules/.cache/turbo
```

#### Debugging Turbo

```bash
# See what turbo is doing
pnpm turbo run build --dry-run

# Verbose output
pnpm turbo run build --verbose

# Show cache hits/misses
pnpm turbo run build --summarize
```

### pnpm Commands Reference

#### Installation

```bash
# Install all dependencies (respects lock file)
pnpm install

# Install with frozen lock file (CI mode)
pnpm install --frozen-lockfile

# Force reinstall everything
pnpm install --force
```

#### Adding Dependencies

```bash
# Add to root workspace
pnpm add -w <package>

# Add to specific package
pnpm --filter <package-name> add <dependency>

# Add with version constraint
pnpm add react@^18.0.0
```

#### Removing Dependencies

```bash
# Remove from specific package
pnpm --filter <package-name> remove <dependency>

# Remove from workspace root
pnpm remove -w <package>
```

#### Workspace Commands

```bash
# Run script in all packages
pnpm -r run build  # -r = recursive

# Run in specific package
pnpm --filter @tennis/backend run test

# Run in packages matching glob
pnpm --filter "./apps/*" run start

# Run with dependencies first
pnpm --filter @tennis/frontend... run build
# ... = include dependencies
```

### Best Practices for pnpm in Worktrees

1. **Keep lock file in sync**

   ```bash
   git pull origin main
   pnpm install
   ```

2. **Clean install for stale worktrees**

   ```bash
   rm -rf node_modules
   rm pnpm-lock.yaml
   git checkout main -- pnpm-lock.yaml
   pnpm install
   ```

3. **Use consistent pnpm version**

   ```json
   {
     "packageManager": "pnpm@9.15.4"
   }
   ```

4. **Ignore temporary files**

   ```gitignore
   .pnpm-install.pid
   .pnpm-debug.log
   node_modules/.cache/
   ```

5. **Use filters for focused work**

   ```bash
   pnpm --filter @tennis/backend... run build
   pnpm turbo run test --filter=[HEAD^1]
   ```

6. **Handle lock file conflicts properly**

   ```bash
   git checkout --theirs pnpm-lock.yaml
   pnpm install
   git add pnpm-lock.yaml
   ```

7. **Clean up before merging**

   ```bash
   rm -f .pnpm-install.pid
   rm -rf node_modules/.cache
   pnpm install
   pnpm run build
   ```

8. **Don't mix npm and pnpm**

   ```bash
   # Always use pnpm in this project
   pnpm install
   ```

### Troubleshooting pnpm Issues

**"EBUSY: resource busy or locked"**

```bash
pkill -f pnpm
rm .pnpm-install.pid
pnpm install
```

**"Cannot find module '@tennis/shared'"**

```bash
pnpm --filter @tennis/shared run build
pnpm run build
```

**"integrity check failed"**

```bash
rm pnpm-lock.yaml
pnpm install
git add pnpm-lock.yaml
```

## Common Git Worktree Workflows

### Workflow 1: Merging Worktree Branch to Main (via PR)

```bash
# 1. Ensure branch is pushed
cd ~/claude-worktrees/tennis-team/my-feature-1234567
git push -u origin HEAD

# 2. Create PR using gh CLI
gh pr create \
  --base main \
  --title "feat: my feature" \
  --body "Summary of changes"

# 3. After merge, clean up worktree
cd /path/to/main/repo
git worktree remove ~/claude-worktrees/tennis-team/my-feature-1234567
git fetch origin --prune
```

### Workflow 2: Syncing Worktree with Main Branch

```bash
# 1. Navigate to worktree
cd ~/claude-worktrees/tennis-team/my-feature-1234567

# 2. Stash any uncommitted changes
git stash push -m "WIP: before sync"

# 3. Fetch latest from origin
git fetch origin main

# 4. Rebase onto main (cleaner history)
git rebase origin/main

# 5. Restore stashed changes
git stash pop

# 6. Rebuild dependencies and code
pnpm install
pnpm run build
```

### Workflow 3: Handling Stale Worktrees

**Option A: Update and Continue Working**

```bash
cd ~/claude-worktrees/tennis-team/my-old-feature

# 1. Fetch latest
git fetch origin --prune

# 2. Rebase or merge latest changes
git rebase origin/main

# 3. Update dependencies (critical for stale worktrees)
pnpm install

# 4. Rebuild everything
pnpm turbo run build --force

# 5. Start Docker infrastructure
pnpm docker:infra

# 6. Run migrations
pnpm --filter @tennis/backend run db:migrate
```

**Option B: Abandon and Remove**

```bash
# From main repo directory
cd /path/to/main/repo

# 1. Remove worktree (even if dirty)
git worktree remove ~/claude-worktrees/tennis-team/my-old-feature --force

# 2. Delete local branch
git branch -D worktree/my-old-feature

# 3. Delete remote branch if exists
git push origin --delete worktree/my-old-feature

# 4. Prune remote references
git fetch origin --prune
```

### Workflow 4: Environment Synchronization

```bash
# Get main repo path
MAIN_REPO=/path/to/main/repo

# Copy environment files
cp $MAIN_REPO/.env .env

# Copy Claude settings
cp $MAIN_REPO/.claude/settings.local.json .claude/settings.local.json
```

**What NOT to Sync**:

- `node_modules/` - Should be installed per worktree
- `dist/`, `build/` - Build artifacts are worktree-specific
- `.pnpm-install.pid` - Process-specific temporary files

### Workflow 5: Safe Worktree Removal

**Before Removing** - Checklist:

```bash
cd ~/claude-worktrees/tennis-team/my-feature

# 1. Check if there are uncommitted changes
git status

# 2. Check if branch is pushed
git log origin/HEAD..HEAD
# Empty = all commits are pushed

# If unpushed commits:
git push origin HEAD
```

**Removal Steps**:

```bash
# From main repo (NOT from worktree directory)
cd /path/to/main/repo

# 1. Remove worktree
git worktree remove ~/claude-worktrees/tennis-team/my-feature

# If worktree is dirty:
git worktree remove ~/claude-worktrees/tennis-team/my-feature --force

# 2. Delete branch (if no longer needed)
git branch -D worktree/my-feature

# 3. Delete remote branch
git push origin --delete worktree/my-feature

# 4. Prune
git fetch origin --prune
git worktree prune
```

### Conflict Resolution Patterns

**1. Package Lock File Conflicts**

```bash
# Accept theirs and regenerate (RECOMMENDED)
git checkout --theirs pnpm-lock.yaml
pnpm install
git add pnpm-lock.yaml
git commit --no-edit
```

**2. .pnpm-install.pid Conflicts**

```bash
# This file should NEVER be committed
rm -f .pnpm-install.pid
git add .pnpm-install.pid 2>/dev/null || true
```

**3. Environment File Conflicts**

```bash
# Keep your worktree's environment
git checkout --ours .env
git add .env
```

**4. Build Artifact Conflicts**

```bash
# Delete and rebuild
rm -rf apps/*/dist packages/*/dist
git add -A
pnpm turbo run build --force
```

### TypeScript Module Resolution Errors After Merge

```bash
# Solution 1: Force Rebuild with Turbo
pnpm turbo run build --force

# Solution 2: Clean Install and Build
rm -rf node_modules
rm -rf apps/*/node_modules packages/*/node_modules
rm -rf apps/*/dist packages/*/dist
pnpm install
pnpm turbo run build --force

# Solution 3: Build Specific Package First
pnpm --filter @tennis/shared run build
pnpm turbo run build
```

### Complete Merge Workflow Example

```bash
# 1. Pre-merge cleanup
rm -f .pnpm-install.pid
git stash push -m "WIP before merge" 2>/dev/null || true

# 2. Fetch and merge
git fetch origin main
git merge origin/main --no-edit

# 3. If conflicts occur:
# Handle lock file
if git diff --name-only --diff-filter=U | grep -q "pnpm-lock.yaml"; then
  git checkout --theirs pnpm-lock.yaml
fi

# Handle .env (keep ours)
if git diff --name-only --diff-filter=U | grep -q ".env"; then
  git checkout --ours .env
fi

# Remove temp files from conflicts
rm -f .pnpm-install.pid
git add pnpm-lock.yaml .env 2>/dev/null || true
git commit --no-edit 2>/dev/null || true

# 4. Reinstall and rebuild
pnpm install
pnpm turbo run build --force

# 5. Restore stashed changes
git stash pop 2>/dev/null || true

# 6. Verify
pnpm run typecheck
echo "Merge complete!"
```

### Best Practices Summary

1. Always remove `.pnpm-install.pid` before merging
2. Use `git checkout --theirs pnpm-lock.yaml` for lock conflicts
3. Keep your `.env` file (use `git checkout --ours .env`)
4. Run `pnpm turbo run build --force` after every merge
5. Use `pnpm install` before building (lock file may have changed)
6. Don't manually edit `pnpm-lock.yaml`
7. Don't commit build artifacts (`dist/`)
8. Don't skip the rebuild step after merging

## Worktree Branch Naming Conventions

```bash
# Feature development
worktree/feature-name-<timestamp>

# Bug fixes
worktree/fix-bug-description-<timestamp>

# Environment-specific
worktree/staging-env-<timestamp>
```

## Troubleshooting Common Issues

**Issue: "fatal: 'branch' is already checked out"**

```bash
# You can't checkout same branch in multiple worktrees
# Solution: Create new branch from it
git worktree add ~/worktrees/new -b new-branch existing-branch
```

**Issue: Worktree directory deleted but git still tracks it**

```bash
git worktree prune
```

**Issue: Cannot remove worktree - "uncommitted changes"**

```bash
git worktree remove ~/claude-worktrees/tennis-team/feature --force
```

**Issue: Merge conflicts in multiple files**

```bash
# Abort and rebase instead
git merge --abort
git fetch origin
git rebase origin/main
```

## Cleanup

When done with a worktree:

1. Push any unpushed commits
2. Remove the worktree: `git worktree remove <path>`
3. Delete the branch: `git branch -D <branch>`
4. If isolated DB was used, drop it: `psql -h localhost -U postgres -c "DROP DATABASE tennis_worktree_<timestamp>;"`
5. Prune: `git worktree prune && git fetch origin --prune`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/tombensim) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
