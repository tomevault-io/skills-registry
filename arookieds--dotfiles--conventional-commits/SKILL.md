---
name: conventional-commits
description: Use this skill ONLY for 'commit', 'save', or 'finish' prompts. Enforces strict manual approval, intelligent Topic-to-Global branching, and hierarchical umbrella management.
metadata:
  author: arookieds
---

# Mandatory Commit & Safety Protocol

## 1. Three-Tier Branching & Umbrella Evolution (MANDATORY)
- **Zero-Commit Rule for Main:** Never commit directly to `main` or `master`.
- **Tier 1: Local Topic Branches (Atomic Changes):**
    1. Analyze changes to propose a specific `topic/` name (e.g., `topic/fix-auth-leak`).
    2. Create a new branch for each atomic unit of work.
    3. Commit changes using Conventional Commits format.
- **Tier 2: Global Umbrella Branches (Feature/Scope):**
    1. When ready to sync, analyze all active `topic/` branches to propose a summarizing `feature/` name (e.g., `feature/security-hardening`).
    2. Merge relevant `topic/` branches into this "Umbrella" branch.
- **Tier 3: Umbrella Evolution (Scope Expansion):**
    1. If new changes make the current `feature/` branch name inaccurate or too narrow:
        - Propose a **NEW** umbrella branch name (e.g., `feature/v1-core-release`).
        - Merge the **OLD** umbrella branch AND any new `topic/` branches into the **NEW** umbrella.
    2. This ensures the branch name always reflects the total scope of changes.

## 2. Pre-Commit Review (Required)
- Before proposing any commit or merge, you **MUST** execute: `bash ./opencode/skill/conventional-commits/scripts/review.sh`.
- Display the output to the user for verification.

## 3. Explicit Commit Types
All commit messages must strictly follow: `<type>(<scope>): <description>`
- **feat**: A new feature.
- **fix**: A bug fix.
- **docs**: Documentation changes.
- **style**: Formatting/styling (no code change).
- **refactor**: Code refactoring.
- **perf**: Performance improvements.
- **test**: Adding/refactoring tests.
- **chore**: Build/maintenance tasks.

## 4. Mandatory Consent (The Barrier)
- **STOP and WAIT:** After showing the review and proposed branch/message, you are strictly forbidden from executing `git commit` or `git merge`.
- Ask: "I have prepared the [topic/umbrella] branch [branch-name]. Should I proceed with the [commit/merge]?"
- Only proceed if the user gives explicit consent.

## 5. Sync Automation
- Use `bash ./opencode/skill/conventional-commits/scripts/git_sync.sh [umbrella-name]` to consolidate branches.
- If evolving an umbrella, ensure the script is called with the new name and handles the merge of the previous umbrella.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/arookieds) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
