---
name: smart-commits
description: Organize and create atomic git commits with intelligent change grouping Use when this capability is needed.
metadata:
  author: akachida
---

Analyze changes, group them into coherent atomic commits, and create signed commits following repository conventions. This command transforms a messy working directory into a clean, logical commit history.

### Grouping Principles

| Principle           | Description                                     |
| ------------------- | ----------------------------------------------- |
| **Feature + Tests** | Implementation and its tests go together        |
| **Config Changes**  | package.json, tsconfig, etc. grouped separately |
| **Documentation**   | README, docs/ changes grouped together          |
| **Refactoring**     | Pure refactors (no behavior change) separate    |
| **Bug Fixes**       | Each fix is atomic with its test                |

### Process Overview

1. **Analyze** - Run `git status` and `git diff` to understand all changes
2. **Group** - Cluster related changes into logical commits
3. **Order** - Determine optimal commit sequence (deps before features, etc.)
4. **Confirm** - Present grouping plan to user for approval
5. **Execute** - Create signed commits in sequence

### Single vs Multiple Commits

**Single commit when:**

- All changes are for one coherent feature/fix
- User provides a specific message via argument
- Changes are minimal and related

**Multiple commits when:**

- Changes span different concerns (feature + docs + deps)
- Mix of features, fixes, and chores
- Better git history benefits future archaeology

### User Confirmation

Before creating commits, present the plan:

```
Proposed Commit Plan:
─────────────────────
1. feat(auth): add OAuth2 refresh token support
   - src/auth/oauth.ts (modified)
   - src/auth/oauth.test.ts (modified)

2. chore(deps): update authentication dependencies
   - package.json (modified)
   - package-lock.json (modified)

3. docs: update OAuth2 setup guide
   - docs/auth/oauth-setup.md (modified)

Proceed with this plan? [Yes / Modify / Single commit]
```

## MANDATORY RULES (NON-NEGOTIABLE)

**These rules MUST be followed for EVERY commit:**

1. **ALWAYS USE conventional commits v1.0.0 to write the messages:**
   - you can find the reference into `../../docs/convetional-commits.md`

2. **Commit message body must be clean and professional:**
   - Only the actual commit description
   - No metadata, signatures, hashtags, or internal references in the body

## Commit Process

### Step 1: Gather Context

Run these commands in parallel to understand the current state:

```bash
# Check staged and unstaged changes
git status

# View ALL changes (staged and unstaged)
git diff
git diff --cached

# View recent commits for style reference
git log --oneline -10
```

### Step 2: Analyze and Group Changes

**For each changed file, determine:**

1. **Type**: feat, fix, chore, docs, refactor, test, style, perf, ci, build
2. **Scope**: Component or area affected (auth, api, ui, etc.)
3. **Logical group**: What other files belong with this change?

**Grouping heuristics:**

**IMPORTANT NOTE:** This example is only for illustration purposes. The actual grouping should be based on the actual changes and programming language standards.

| File Pattern                  | Likely Group                   |
| ----------------------------- | ------------------------------ |
| `*.test.ts`, `*.spec.ts`      | Group with implementation file |
| `package.json`, `*-lock.json` | Dependency changes             |
| `*.md`, `docs/*`              | Documentation                  |
| `*.config.*`, `tsconfig.*`    | Configuration                  |
| Same directory/module         | Often related                  |

**Create a mental (or actual) grouping:**

```
Group 1 (feat): auth changes
  - src/auth/oauth.ts
  - src/auth/oauth.test.ts

Group 2 (chore): dependencies
  - package.json
  - package-lock.json

Group 3 (docs): documentation
  - README.md
```

### Step 3: Determine Commit Order

**Order matters for bisectability:**

1. **Dependencies first** - So subsequent commits can use them
2. **Core changes** - Implementation before consumers
3. **Tests with implementation** - Keep them atomic
4. **Documentation last** - Documents the final state

### Step 4: Present Plan and Confirm

**MANDATORY: Get user confirmation before executing.**

- "I've analyzed your changes and propose this commit plan. How should I proceed?",
  1.  Execute plan
  2.  Single commit
  3.  Let me review it

If user selects "Let me review", show the full plan with files per commit.

### Step 5: Execute Commits (Signed)

**For each commit group, in order:**

**Stage only the files for this commit:**

```bash
git add <file1> <file2> ...
```

### Step 6: Verify Commits

After all commits, verify the result:

```bash
# Show all new commits
git log --oneline -<number_of_commits>

# Confirm clean state
git status
```

## Important Notes

1. **Smart grouping** - Analyzes changes and proposes atomic commits for clean history
2. **No visible markers** - The message body stays clean and professional
3. **Transparent** - System tracing is documented, just not prominently displayed
4. **Do not use --no-verify** - Always run pre-commit hooks unless user explicitly requests
5. **User confirmation** - Always present commit plan before executing

## When User Provides Message

If the user provides a commit message as argument:

1. **Single commit mode** - Skip grouping analysis, use provided message
2. Use their message as the subject/body
3. Ensure proper formatting (50 char subject, etc.)
4. Create signed commit with trailer

## Step 7: Offer Push (Optional)

After successful commit, ask the user if they want to push:

- Validate if the branch is already tracking a remote branch then:

```bash
git push
```

- If branch has no upstream, use:

```bash
git push -u origin <current-branch>
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/akachida) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
