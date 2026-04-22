---
name: git-ops
description: Comprehensive git workflow including worktree isolation, branching, and branch finalization. Use when this capability is needed.
metadata:
  author: ssimhan
---

# Unified Git Operations

## Phase 1: Isolation (Worktrees)
Use this when starting new features or executing implementation plans.

1. **Directory Selection**:
   - Check `.worktrees/` (preferred) or `worktrees/`.
   - If neither exists, check `CLAUDE.md` for a preference or ask the user.
2. **Safety Verification**:
   - MUST verify the worktree directory is ignored in `.gitignore`. If not, add it and commit the change immediately.
3. **Creation**:
   - `git worktree add <path> -b <branch-name>`
   - Run project setup (e.g., `npm install`, `pip install`).
4. **Baseline Verification**:
   - Run tests to ensure the workspace starts in a clean state (Green).

## Phase 2: Implementation & Commits
- Use atomic commits: `[Action] [Scope]: [Specific change]`.
- One logical change per commit.

## Phase 3: Finalization
Use this when implementation is complete and all tests pass.

1. **Verify Tests**: Run the full test suite one last time.
2. **Present Options**:
   - **1. Merge locally**: Checkout base, pull, merge, verify, delete feature branch, remove worktree.
   - **2. Create PR**: Push branch, run `gh pr create` with summary and test plan.
   - **3. Keep as-is**: Preserves branch and worktree for later.
   - **4. Discard**: Require explicit "discard" confirmation before deleting branch and worktree.
3. **Cleanup**: Only remove the worktree for options 1, 2, and 4 (if PR is pushed and worktree no longer needed).

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ssimhan) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
