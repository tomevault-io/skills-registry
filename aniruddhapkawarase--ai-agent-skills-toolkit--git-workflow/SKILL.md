---
name: git-workflow
description: Production git workflows covering trunk-based development vs GitFlow, conventional commits, semantic versioning automation, branch protection rules, merge strategies (squash/rebase), cherry-pick workflows, bisect debugging, and monorepo strategies. Use when this capability is needed.
metadata:
  author: AniruddhaPKawarase
---

# Production Git Workflows (30+ Year Veteran)

## Two Dominant Models: Trunk-Based vs GitFlow

### Trunk-Based Development (Recommended for High-Velocity Teams)
Everyone commits to main/trunk. Short-lived feature branches (<1 day).

```
main ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
      ↑      ↑      ↑      ↑      ↑
    f1-pr  f2-pr  f3-pr  f4-pr  f5-pr
     (merged)
```

**Process**:
```bash
# 1. Create feature branch from main (not from old branch!)
git checkout main && git pull origin main
git checkout -b feature/user-auth

# 2. Small commits, frequent pushes
git add src/auth.py
git commit -m "feat: Add JWT token validation"
git push origin feature/user-auth

# 3. Open PR, merge to main (not develop/staging)
git checkout main && git pull origin main
git merge --squash feature/user-auth
git push origin main

# Delete branch
git branch -d feature/user-auth
git push origin --delete feature/user-auth
```

**Pros**:
- ✓ Fast feedback loops (PR → merge in hours, not days)
- ✓ Less merge conflicts (short-lived branches)
- ✓ Cleaner main history
- ✓ Easier to understand what's deployed (main = production)

**Cons**:
- ✗ Requires strong CI/CD (catch bugs before merge)
- ✗ Requires feature flags (incomplete features don't go live)

### GitFlow (Recommended for Scheduled Releases)
main = releases, develop = next release, feature/release/hotfix branches.

```
main    ━━━━v1.0━━━━━v1.1━━━━━━━━━v1.2━━
             ↑         ↑          ↑
develop ━━━━╋━━━━━━━━╋━━━━━━━━━╋━━━━
       ↑    ↑  ↑   ↑  ↑   ↑  ↑
    f1 f2  f3    f4    f5
```

**Process**:
```bash
# Feature: Branch from develop
git checkout develop && git pull origin develop
git checkout -b feature/user-auth

# ... commit, push, PR to develop ...

# Release: Branch from develop when ready for release
git checkout develop && git pull origin develop
git checkout -b release/1.1.0

# Only bugfixes, version bumps, no new features
git tag v1.1.0
git push origin release/1.1.0

# Merge to main (production)
git checkout main && git pull origin main
git merge --no-ff release/1.1.0
git push origin main

# Merge back to develop (in case of release hotfixes)
git checkout develop
git merge --no-ff main
git push origin develop

# Hotfix: Branch from main (emergency production fix)
git checkout main && git pull origin main
git checkout -b hotfix/1.1.1

# Fix bug, test thoroughly
git merge --no-ff main  # Merge to main
git tag v1.1.1
git checkout develop && git merge --no-ff hotfix/1.1.1  # Also merge back
```

**Pros**:
- ✓ Clear release schedule
- ✓ Can plan scheduled deployments
- ✓ Easy to support multiple versions (1.0, 1.1, 2.0)

**Cons**:
- ✗ Complex branching (more merge conflicts)
- ✗ Slower release cycles (feature accumulation, then big release)
- ✗ Harder to track what's deployed where

**My Recommendation**: Start with trunk-based. Use GitFlow only if you have scheduled releases (SaaS with quarterly releases, mobile apps with app store review cycles).

## Conventional Commits (Semantic Versioning Ready)

Format: `<type>(<scope>): <subject>`

```
feat(auth): Add JWT token refresh endpoint
fix(api): Handle payment timeout gracefully
refactor(database): Extract query builder into service
docs(readme): Add deployment instructions
test(cart): Add unit tests for discount calculation
chore(deps): Upgrade fastapi to 0.104
perf(inventory): Add index on product_id for 10x query speedup
```

**Why it matters**:
- Enables automatic changelog generation
- Enables semantic versioning (major.minor.patch)
- Makes git log readable (git log --oneline tells the story)

**Types**:
- `feat`: New feature (minor version bump)
- `fix`: Bug fix (patch version bump)
- `refactor`: Internal improvement (no version bump)
- `docs`: Documentation only
- `test`: Test additions/fixes
- `chore`: Dependency updates, tooling
- `perf`: Performance improvements
- `BREAKING CHANGE`: Major version bump (note in footer)

```bash
# Example: Breaking change (major version)
feat(api): Change /orders response schema

BREAKING CHANGE: /orders now returns { data: [...] } instead of [...]
Clients must update to expect nested response.
```

## Semantic Versioning Automation

```yaml
# In GitHub Actions: Auto-bump version on merge to main
name: Release

on:
  push:
    branches: [main]

jobs:
  release:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - uses: actions/setup-node@v3

      - name: Bump version and create tag
        run: |
          npm install -g semantic-release
          npx semantic-release

      # Version automatically determined by commit types:
          # feat → 0.1.0
          # fix → 0.0.1
          # BREAKING CHANGE → 1.0.0
```

## Branch Protection Rules (GitHub)

```markdown
# Required for production safety

## Branch: main
- [ ] Require a pull request before merging
  - Dismiss stale pull request approvals
  - Require approval from code owners
- [ ] Require status checks to pass before merging
  - Tests must pass
  - Linter must pass
  - Security scan must pass
- [ ] Require branches to be up to date before merging
  - Prevents merging stale branches
- [ ] Require conversation resolution
  - All comments must be resolved before merge
- [ ] Restrict who can push to main
  - Only release engineer, automated tools
- [ ] Enforce all status checks
  - Can't skip checks even with admin

## Branch: develop
- [ ] Require a pull request before merging
- [ ] Require status checks (but allow override by maintainers)
- [ ] Auto-delete branch on merge
```

## Merge Strategies: Squash vs Rebase vs Merge

### Squash (Recommended for features)
Combines all feature commits into single commit on main.

```bash
# Feature branch: 5 commits
feature/user-auth:
  - c1: Add User model
  - c2: Add password validation
  - c3: Fix password validation edge case
  - c4: Add JWT token generation
  - c5: Fix import

# After squash merge to main: 1 commit
main:
  - abc123: feat(auth): Add JWT authentication
```

**Pros**: Clean history, logical commits
**Cons**: Lose detail history of feature development

### Rebase (Recommended for small fixes)
Replays feature commits on top of main.

```bash
# Feature branch (before rebase)
main    ━━━━abc123━━
       ↑
feat   ━━c1━━c2━━c3

# After rebase onto main
main    ━━━━abc123━━━━c1'━━c2'━━c3' (new commits, not c1-c3)

# After merge
main    ━━━━abc123━━━━c1'━━c2'━━c3'
```

**Pros**: Clean linear history, all commits visible
**Cons**: Rewrites history (confusing if others use the branch)

### Merge (Traditional)
Creates merge commit, preserves both branches.

```bash
# Feature branch
main    ━━━━abc123━━━━M (merge commit) ━━
       ↑              ↗
feat   ━━c1━━c2━━c3 ↗

# After merge
main    ━━abc123━━M━━ (explicit merge point)
                 ↙↖
              c1, c2, c3 (still visible)
```

**Pros**: Explicit merge point, easy to revert entire feature
**Cons**: Cluttered history (lots of merge commits)

**GitHub Recommendation**:
```
- Use squash for feature PRs (clean main history)
- Use rebase for hotfixes (minimal, linear)
- Rarely use merge (old-school)
```

## Cherry-Pick Workflow (Backporting to Release Branch)

Bug found in main, need to fix in currently-deployed version.

```bash
# Scenario: Bug fixed in main (v2.0-dev), but v1.5 is in production
# Production is on release/1.5 branch

# 1. Find commit with fix
git log main | grep "payment bug"  # Found: abc123

# 2. Cherry-pick to release branch
git checkout release/1.5
git cherry-pick abc123

# 3. Verify fix compiles and tests pass
git log --oneline -3  # Should show cherry-picked commit

# 4. Create backport PR for review
git push origin release/1.5
# PR: Cherry-pick fix to 1.5

# 5. After merge, tag hotfix version
git checkout release/1.5 && git pull
git tag v1.5.1
git push origin v1.5.1

# Deploy v1.5.1 to production while v2.0 continues in main
```

## Git Bisect: Finding Bugs via Binary Search

```bash
# Symptom: Feature broke sometime in last 100 commits
git bisect start
git bisect bad HEAD  # Current commit is broken
git bisect good HEAD~100  # Commit 100 commits ago was fine

# Git checks out middle commit (~commit 50)
# Run tests: Is this commit broken?

git bisect bad  # This commit is broken, search older commits
# Git checks out ~commit 25

git bisect good  # This commit is fine, search newer commits
# Git checks out ~commit 37

git bisect bad  # This commit is broken
# ... binary search continues ...

# Eventually finds: abc123 introduced the bug
git show abc123  # Inspect the breaking commit

git bisect reset  # Done, back to original branch
```

**Automated bisect** (if you have a failing test):
```bash
git bisect run pytest tests/payment_test.py::test_charge_idempotency

# Git automatically runs your test on each bisect attempt
# Stops at first broken commit
```

## Interactive Rebase: Squashing Commits

```bash
# You have: c1, c2, c3 (ready to merge, but want single commit)
git log --oneline -3
# c3 Fix typo
# c2 Add payment handler (incomplete)
# c1 Add payment service

# Interactive rebase
git rebase -i HEAD~3

# Opens editor:
# pick c1 Add payment service
# pick c2 Add payment handler (incomplete)
# pick c3 Fix typo

# Change to:
# pick c1 Add payment service
# squash c2 Add payment handler (incomplete)
# squash c3 Fix typo

# Save, editor opens with combined commit message
# Edit message:
# feat(payment): Add payment processing service

# Result: Single commit with all changes
git log --oneline -1
# abc123 feat(payment): Add payment processing service
```

## Monorepo Strategies

**Monorepo = Single repo with multiple services/packages**

```
my-monorepo/
├─ services/
│  ├─ api/          (FastAPI backend)
│  ├─ worker/       (Celery workers)
│  └─ admin/        (Django admin)
├─ packages/
│  ├─ auth/         (Shared auth library)
│  ├─ database/     (Shared DB models)
│  └─ utils/        (Common utilities)
└─ infrastructure/
   ├─ docker/
   ├─ terraform/
   └─ kubernetes/
```

**Challenge**: Which services changed in a commit?

```bash
# Tooling: Nx, TurboRepo, Bazel help answer this

# Manual approach: Changelog by path
git log --oneline --name-only | grep "^services/api/"  # Changes to API

# CI/CD should rebuild only changed services
if git diff --quiet main...HEAD services/api; then
  echo "API unchanged, skip rebuild"
else
  docker build -t api:latest services/api
fi
```

## Workflow Checklist

```markdown
# Git Workflow Checklist

## Before Starting Work
- [ ] Pull latest main: `git pull origin main`
- [ ] Create feature branch: `git checkout -b feature/my-feature`

## During Development
- [ ] Commit frequently, with clear messages: `git commit -m "feat(auth): Add token refresh"`
- [ ] Push regularly: `git push origin feature/my-feature`
- [ ] Stay updated: `git rebase origin/main` if main has new commits

## Before PR
- [ ] Tests passing locally: `pytest`
- [ ] Linter passing: `ruff check .`
- [ ] No merge conflicts: `git merge-base --is-ancestor origin/main HEAD`

## PR Review
- [ ] Approval from >=1 reviewer
- [ ] All checks passing (tests, lint, security scan)
- [ ] Squash merge to main

## Post-Merge
- [ ] Delete feature branch locally: `git branch -d feature/my-feature`
- [ ] Verify deployment: Check staging environment

## Releases
- [ ] Tag version: `git tag v1.2.3`
- [ ] Push tag: `git push origin v1.2.3`
- [ ] CI/CD deploys and creates release notes
```

## War Stories

**Lost Commits**: Deleted feature branch, commits orphaned. Recovered with `git reflog`.

**Merge Conflict Nightmare**: Rebased main while developer was working on same branch. Forced him to resolve massive conflicts. Now: communicate rebases.

**Bad Squash**: Accidentally squashed unrelated commits. Reverted with `git revert` and redid the merge.

**Monorepo Versioning**: Service A v2.0, Service B still uses v1.0 API. Now: each service has version tag (v-api/2.0.0 vs v-worker/1.5.0).

## Summary
Version control is not just for backups. Git workflows enable collaboration, automation, and auditability. Choose trunk-based for velocity or GitFlow for scheduled releases. Use conventional commits for automation. Protect main with branch rules. Squash feature commits. Cherry-pick hotfixes. Bisect to find regressions. Make your commit history readable—you'll thank yourself later.

---
> Source: [AniruddhaPKawarase/ai-agent-skills-toolkit](https://github.com/AniruddhaPKawarase/ai-agent-skills-toolkit) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
