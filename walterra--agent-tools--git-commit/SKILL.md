---
name: git-commit
description: Commit staged files with a precise, well-crafted commit message. Use when the user asks to commit, make a commit, or says "commit". Handles pre-commit hook failures, lint fixes, and safe re-staging. Use when this capability is needed.
metadata:
  author: walterra
---

# Git Commit

Commit currently staged files with an accurate, concise commit message.

## Workflow

### Step 1: Identify staged files

Record the list of staged files. This list is the **source of truth** for the entire workflow.

```bash
git diff --cached --name-only
```

If nothing is staged, inform the user and stop.

### Step 2: Analyze changes and commit

Analyze the actual diff to understand what changed:

```bash
git diff --cached
```

Then commit with a one-liner message:

```bash
git commit -m "<message>"
```

#### Commit message rules

- **Max 60 characters.**
- **Describe what was done accurately.** Analyze the diff — don't default to generic verbs like "add" when files were modified, updated, or refactored.
- **No credit information.** No "generated with", "co-authored-by", or similar.
- **Conventional commits / semantic prefixes** — only use them if the project already uses them (check recent `git log --oneline -10`).

### Step 3: Check for pre-commit hook failures

If the commit succeeded, you're done. If pre-commit hooks failed (linting, type-checking, formatting), continue below.

### Step 3.1: Auto-fix with project tooling

Run the project's own maintenance commands to fix issues automatically:

```bash
# Examples — use whatever the project has
pnpm lint --fix
pnpm format
pnpm tsc:check
npm run lint
```

### Step 3.2: Fix remaining issues manually

If auto-fix didn't resolve everything, fix the remaining issues by hand.

### Step 3.3: Re-stage the original files (with a narrow exception)

**CRITICAL: Re-stage only files from the list captured in Step 1 — except for new files that are required to satisfy linting rules.**

```bash
git add <file-from-step-1>
```

If linting/formatting requires refactoring into helper files (e.g., max file length, complexity rules), you **may** stage newly created files **only when**:

- The new files are direct refactors of the originally staged changes, and
- They are necessary to make linting pass without deleting content or taking shortcuts.

### Step 3.4: Never stage unrelated extra files

- **NEVER** use `git add .` or any bulk staging command.
- If a formatter or linter modified a file that was **not** in the Step 1 list **and** is not a required new helper file, leave it unstaged.

**Example:** If only `A.ts` was originally staged, but linting requires extracting utilities into `helpers.ts`, you may stage `helpers.ts` along with `A.ts`. If a formatter also touched `B.ts`, leave `B.ts` unstaged.

### Step 3.5: Retry the commit

```bash
git commit -m "<message>"
```

Use the same message from Step 2 (or refine it if fixes changed the scope).

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/walterra) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-12 -->
