---
name: ralph
description: Convert PRDs to prd.json format for the Ralph autonomous agent system. Use when you have an existing PRD and need to convert it to Ralph's JSON format. Triggers on convert this prd, turn this into ralph format, create prd.json from this, ralph json, set up ralph for this project, initialize ralph project. Use when this capability is needed.
metadata:
  author: hellanglez
---

# Ralph PRD Generator

This skill converts Product Requirements Documents (PRDs) into Ralph's structured `prd.json` format and initializes the Ralph autonomous agent system.

## What Ralph Does

Ralph is an autonomous coding agent that:
1. Reads a `prd.json` file with structured user stories
2. Implements stories one at a time, running quality checks
3. Commits passing code and updates progress
4. Continues until all stories are complete

## Usage

When a user provides a PRD (in any format - markdown, text, bullet points), convert it to Ralph's format and set up the project.

## Steps

### 1. Analyze the PRD

Extract:
- Project name and description
- User stories (features to implement)
- Acceptance criteria for each story
- Dependencies between stories

### 2. Generate prd.json

Create a `prd.json` file with this structure:

```json
{
  "project": "Project Name",
  "description": "Brief project description",
  "branchName": "main",
  "userStories": [
    {
      "id": "STORY-001",
      "title": "Story title",
      "description": "What needs to be implemented",
      "acceptanceCriteria": [
        "Criterion 1",
        "Criterion 2"
      ],
      "priority": 1,
      "passes": false
    }
  ]
}
```

**Story ID format**: `STORY-001`, `STORY-002`, etc.

**Priority**: Lower numbers = higher priority (1 is highest)

**passes**: Always start with `false` (Ralph will set to `true` after implementing)

### 3. Create progress.txt

Initialize with:

```
## Codebase Patterns
(This section will be populated by Ralph as it learns about the codebase)

---
```

### 4. Verify Ralph Installation

Check if Ralph is installed:

```bash
ls ~/.codex/ralph/ralph.sh
```

If not installed, inform the user:

```
Ralph is not installed. To install:

cd /path/to/burn-in-cceverywhere-codex
./install.sh

Or install directly:
git clone -b v2 https://github.com/hellangleZ/burn-in-cceverywhere-codex.git
cd burn-in-cceverywhere-codex
./install.sh
```

### 5. Provide Usage Instructions

After generating the files, tell the user:

```
✅ Ralph project initialized!

Files created:
- prd.json (X user stories)
- progress.txt (initialized)

To start Ralph:

# Run once
~/.codex/ralph/ralph.sh

# Run in background
~/.codex/ralph/ralph.sh --bg

# View logs
~/.codex/ralph/view-logs.sh 1 -s    # Summary mode
~/.codex/ralph/view-logs.sh 1 -c    # Conversation mode

Ralph will:
1. Read prd.json
2. Implement STORY-001 first (highest priority)
3. Run quality checks (tests, lint, typecheck, build)
4. Commit if passing
5. Continue to next story
```

## PRD Conversion Examples

### Example 1: Simple Markdown PRD

**Input:**
```
# Todo App

Features:
- Add todos
- Mark todos as done
- Delete todos
- Filter by status
```

**Output prd.json:**
```json
{
  "project": "Todo App",
  "description": "A simple todo list application",
  "branchName": "main",
  "userStories": [
    {
      "id": "STORY-001",
      "title": "Add todos",
      "description": "Users can add new todo items to the list",
      "acceptanceCriteria": [
        "User can enter todo text",
        "Todo appears in the list after adding",
        "Empty todos are not allowed"
      ],
      "priority": 1,
      "passes": false
    },
    {
      "id": "STORY-002",
      "title": "Mark todos as done",
      "description": "Users can mark todo items as completed",
      "acceptanceCriteria": [
        "User can click/tap to toggle done status",
        "Completed todos show visual indicator",
        "Done status persists"
      ],
      "priority": 2,
      "passes": false
    },
    {
      "id": "STORY-003",
      "title": "Delete todos",
      "description": "Users can remove todo items from the list",
      "acceptanceCriteria": [
        "User can delete individual todos",
        "Deleted todos are removed from the list",
        "Deletion is confirmed before removing"
      ],
      "priority": 3,
      "passes": false
    },
    {
      "id": "STORY-004",
      "title": "Filter by status",
      "description": "Users can filter todos by completion status",
      "acceptanceCriteria": [
        "Filter options: All, Active, Completed",
        "List updates when filter changes",
        "Filter selection is preserved"
      ],
      "priority": 4,
      "passes": false
    }
  ]
}
```

### Example 2: Detailed PRD with Dependencies

When user stories have dependencies, use priority to enforce order:

```json
{
  "userStories": [
    {
      "id": "STORY-001",
      "title": "User authentication",
      "priority": 1,
      "passes": false
    },
    {
      "id": "STORY-002",
      "title": "User profile page",
      "description": "Depends on STORY-001 (auth must exist first)",
      "priority": 2,
      "passes": false
    }
  ]
}
```

## Quality Expectations

Ralph expects projects to have quality checks:
- **Tests**: `npm test` or equivalent
- **Lint**: `npm run lint` or equivalent
- **TypeScript**: `npm run typecheck` (if applicable)
- **Build**: `npm run build` or equivalent

If the project doesn't have these yet, Ralph will set them up during STORY-001.

## Tips

1. **Break down large features** - Each story should be completable in one iteration (15-30 minutes)
2. **Clear acceptance criteria** - Be specific about what "done" means
3. **Logical ordering** - Use priority to sequence dependent stories
4. **Testable stories** - Each story should have verifiable acceptance criteria

## Related Files

Ralph uses:
- `prd.json` - User stories and progress tracking
- `progress.txt` - Iteration logs and codebase patterns
- `AGENTS.md` or `CLAUDE.md` - Project-specific agent instructions (optional)

## Branch Management

Ralph respects the `branchName` field in prd.json. To work on a feature branch:

```json
{
  "branchName": "feature/new-ui",
  "userStories": [...]
}
```

Ralph will checkout or create this branch before starting work.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/hellanglez) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
