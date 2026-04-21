---
name: task-implementation
description: Implement engineering tasks from task specifications using TDD in isolated git worktrees. Use when user wants to implement a task from docs/tasks/, build a feature from TASKS.md, code a specific epic/story/task, or says "implement task X", "build task X", "work on task X". Reads task specs, PRD, and architecture docs for context, creates a feature branch worktree, writes tests first, implements code, verifies coverage, updates docs, and creates a PR. Use when this capability is needed.
metadata:
  author: porkbutts
---

# Task Implementation

Implement engineering tasks in isolated git worktrees using a test-first workflow. Read the task spec, build in a clean branch, write tests, implement, verify, and open a PR.

## Workflow

```
START
  │
  ▼
┌──────────────────────────────┐
│ 1. Load Task Specification   │ ◄── docs/tasks/task-<id>.md or docs/TASKS.md
└──────────┬───────────────────┘
           │
           ▼
┌──────────────────────────────┐
│ 2. Load Project Context      │ ◄── PRD, Architecture, Brand Guidelines
└──────────┬───────────────────┘
           │
           ▼
┌──────────────────────────────┐
│ 3. Create Worktree & Branch  │ ◄── git worktree in .worktrees/
└──────────┬───────────────────┘
           │
           ▼
┌──────────────────────────────┐
│ 4. Implement (test-first)    │ ◄── Write tests → Write code → Pass tests
└──────────┬───────────────────┘
           │
           ▼
┌──────────────────────────────┐
│ 5. Verify                    │ ◄── Tests pass, coverage thresholds met
└──────────┬───────────────────┘
           │
           ▼
┌──────────────────────────────┐
│ 6. Update Documentation      │ ◄── TASKS.md, README if appropriate
└──────────┬───────────────────┘
           │
           ▼
┌──────────────────────────────┐
│ 7. Push & Create PR          │ ◄── Push branch, open PR via gh
└──────────────────────────────┘
```

## Step 1: Load Task Specification

Read the task document. Identify which to load:

| Input | Source |
|-------|--------|
| User provides task ID (e.g., "task 3.1.2") | Read `docs/tasks/task-3.1.2.md` |
| User references epic/story (e.g., "story 2.1") | Read `docs/tasks/task-2.1.md`, or find matching section in `docs/TASKS.md` |
| No individual task doc exists | Fall back to `docs/TASKS.md`, locate the relevant section |

Extract from the task spec:
- **Title and ID** — for branch naming and PR title
- **Files to create/modify** — scope of changes
- **Acceptance criteria** — drives test cases
- **Testing instructions** — how to verify
- **Dependencies** — other tasks that must be complete first

If the task has unmet dependencies, warn the user before proceeding.

## Step 2: Load Project Context

Read these documents for implementation context:

| Document | When to Read |
|----------|--------------|
| `docs/PRD.md` | Always — understand product intent |
| `docs/ARCHITECTURE.md` | Always — understand tech stack, directory structure, key abstractions |
| `docs/BRAND-GUIDELINES.md` | When task involves UI components, styling, or visual elements built from scratch |
| `docs/TASKS.md` | When you need to understand ordering, dependencies, or broader scope |

Also scan the existing codebase to understand:
- Project structure and conventions (naming, patterns, file organization)
- Existing test patterns (framework, helpers, mocking approach)
- Package manager (check lock files: `pnpm-lock.yaml`, `yarn.lock`, `bun.lockb`, `package-lock.json`)

## Step 3: Create Worktree & Branch

Create an isolated worktree for the feature branch:

```bash
# Branch naming: feature/task-<id>-<short-description>
# Example: feature/task-3.1.2-login-form

BRANCH_NAME="feature/task-<id>-<short-description>"
WORKTREE_DIR=".worktrees/$BRANCH_NAME"

git worktree add -b "$BRANCH_NAME" "$WORKTREE_DIR"
```

All subsequent work happens inside the worktree directory. Change your working directory there.

Install dependencies in the worktree if needed (e.g., `npm install`, `pnpm install`).

## Step 4: Implement (Test-First)

Follow this cycle for each piece of functionality:

### 4a. Write Tests First

From the acceptance criteria, write test cases that will fail:

- One test per acceptance criterion minimum
- Include edge cases mentioned in the task spec
- Include a happy path and at least one error/boundary case
- Follow existing test conventions in the project (framework, file location, naming)

Run the tests to confirm they fail (red phase).

### 4b. Write Implementation

Implement the minimum code to pass the tests:

- Follow patterns from `docs/ARCHITECTURE.md` (directory structure, key abstractions)
- Match existing code conventions (imports, naming, error handling)
- If the task involves UI and `docs/BRAND-GUIDELINES.md` exists, follow its design tokens and patterns
- Keep changes scoped to files listed in the task spec — avoid unrelated modifications

### 4c. Pass Tests

Run the test suite. Iterate on implementation until all tests pass (green phase).

### 4d. Refactor

Clean up implementation while keeping tests green. Remove duplication, improve naming, extract constants. Do not add functionality beyond what the tests cover.

### 4e. Commit

Make atomic commits as logical units of work complete. Use conventional commit messages:

```
feat(scope): short description of what was added
test(scope): add tests for <feature>
```

## Step 5: Verify

Run full verification before considering the task done:

```bash
# Run full test suite (not just new tests)
<package-manager> test

# Check coverage thresholds pass
<package-manager> test -- --coverage

# Lint passes
<package-manager> run lint

# Type check passes (TypeScript projects)
<package-manager> run typecheck

# Build succeeds
<package-manager> run build
```

All checks must pass. If coverage thresholds are configured in the project (e.g., in `vitest.config.ts` or `jest.config.ts`), ensure they are met. If no thresholds are configured, aim for:

| Metric | Minimum |
|--------|---------|
| Statements | 80% |
| Branches | 80% |
| Functions | 80% |
| Lines | 80% |

If any check fails, fix the issue and re-run. Do not proceed with failures.

## Step 6: Update Documentation

### Mark Task Complete in TASKS.md

In `docs/TASKS.md`, mark the completed task:
- Change `- [ ]` to `- [x]` for completed acceptance criteria
- Mark the task checkbox as complete
- If all tasks in a story are done, mark the story complete too

### Update README (When Appropriate)

Update the project README if the task:
- Adds a new user-facing feature or command
- Changes setup/installation steps
- Adds new environment variables or configuration
- Changes API endpoints

Do not update README for internal refactors, test additions, or implementation details.

## Step 7: Push & Create PR

```bash
git push -u origin "$BRANCH_NAME"
```

Create a PR using `gh`:

```bash
gh pr create --title "<type>(task-<id>): <short title>" --body "$(cat <<'EOF'
## Summary

Implements task <id>: <task title>

### Changes
- <bullet points of what was done>

### Acceptance Criteria
- [x] <criterion 1>
- [x] <criterion 2>

## Test Plan

### Automated
- <commands to run, e.g. `pnpm test`, `pnpm run lint`>

### Manual QA
- [ ] <visual/interactive verification step for a human or QA agent with browser access>
- [ ] <another manual check if applicable>

## Task Reference
- Task spec: `docs/tasks/task-<id>.md`

🤖 Generated with [Claude Code](https://claude.com/claude-code)
EOF
)"
```

After PR creation, report the PR URL to the user.

## Error Recovery

| Problem | Action |
|---------|--------|
| Dependency not installed | Run package manager install in worktree |
| Tests fail after implementation | Debug, fix, re-run — do not skip tests |
| Coverage below threshold | Add missing test cases for uncovered branches |
| Lint/typecheck fails | Fix issues before committing |
| Worktree already exists for branch | Ask user: reuse existing worktree or pick a new branch name |
| Task has unmet dependencies | Warn user, ask whether to proceed or implement dependency first |

## Cleanup

After the PR is merged (not before), the worktree can be removed:

```bash
git worktree remove ".worktrees/$BRANCH_NAME"
git branch -d "$BRANCH_NAME"
```

Do not clean up automatically — only when the user requests it or confirms the PR is merged.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/porkbutts) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
