---
name: parallel-development
description: Git worktree-based parallel development workflow for BMAD projects. Use when setting up parallel story development, creating worktrees for multiple stories, managing multi-agent Claude Code sessions, or organizing stories for parallel execution. Triggers include "parallel development", "worktree", "parallel stories", "multi-agent development", "parallel set", or requests to develop multiple stories simultaneously. Use when this capability is needed.
metadata:
  author: thbst16
---

# Parallel Development Skill

Enables parallel story implementation using Git worktrees with multiple Claude Code sessions. Each story runs in an isolated worktree with its own terminal, preventing merge conflicts through module boundary separation.

## Configuration

Check for `CONFIG.md` in this skill directory for project-specific settings. If not present, prompt for:
- `PROJECT_PREFIX`: Worktree naming prefix (e.g., "myproject")
- `REPO_PATH`: Absolute path to main repository
- `STORY_FORMAT`: Story ID format (default: `E{epic}-S{story}`)

## Quick Start

### 1. Generate Parallel Development Plan
Use `references/create-stories-prompt.md` with the SM agent to analyze PRD/Architecture and generate a parallelization plan.

### 2. Create Workplan
Copy `references/workplan-template.md` to project's `_bmad-output/parallel-development-plan.md` and populate with generated stories.

### 3. Create Workflow
Copy `references/workflow.md` to project's `_bmad-output/parallel-development-workflow.md` and customize paths.

### 4. Execute Parallel Set
```bash
# Setup worktrees for a parallel set
./scripts/parallel-dev.sh setup <set-number>

# Open separate terminals, one per worktree
# Run Claude Code in each: claude

# After completion, cleanup
./scripts/parallel-dev.sh cleanup <set-number>
```

## Core Workflow (8 Phases)

**Phase 0: Prerequisites** - Foundation stories complete on main

**Phase 1: Prepare Stories** - Create story files using BMAD create-story workflow on main branch

**Phase 2: Create Worktrees** - Branch and worktree per story using `parallel-dev.sh setup`

**Phase 3: Parallel Implementation** - One Claude Code terminal per worktree, one story per agent

**Phase 4: Per-Story Code Review** - Run code-review in each worktree

**Phase 5: Integration Branch** - Merge all feature branches to `integration/parallel-set-N`

**Phase 6: Integration Review** - Combined review, full test suite

**Phase 7: Pull Request** - PR from integration branch to main

**Phase 8: Cleanup** - Remove worktrees, delete branches, update sprint status

## Critical Rules

1. **ONE AGENT = ONE STORY** - Never work on multiple stories in a single session
2. **PHASES ARE SEQUENTIAL** - Complete each phase before proceeding
3. **PHASE 1 ON MAIN** - Story files must be created on main before worktrees
4. **CLEANUP IS MANDATORY** - Phase 8 must complete before next parallel set

## References

- **Full Workflow**: `references/workflow.md` - Complete 8-phase process with commands
- **Workplan Template**: `references/workplan-template.md` - Parallel set organization template
- **Story Prompt**: `references/create-stories-prompt.md` - Prompt for generating parallelization plan

## Script Usage

```bash
# View available commands
./scripts/parallel-dev.sh help

# Setup parallel set (creates branches + worktrees)
./scripts/parallel-dev.sh setup 1

# List current worktrees
./scripts/parallel-dev.sh status

# Cleanup after merge (removes worktrees + branches)
./scripts/parallel-dev.sh cleanup 1

# Create integration branch and merge features
./scripts/parallel-dev.sh integrate 1
```

## Story ID Format

Default format: `E{epic}-S{story}` (e.g., E2-S1, E3-S4)

- **E** = Epic number
- **S** = Story number within epic

Branch naming: `feature/{story-slug}` (e.g., `feature/brand-model`)

Worktree naming: `../{PROJECT_PREFIX}-{story-id}` (e.g., `../myproject-E2-S1`)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/thbst16) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
