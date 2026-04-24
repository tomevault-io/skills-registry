---
name: verify
description: | Use when this capability is needed.
metadata:
  author: howells
---

<tool_restrictions>
# MANDATORY Tool Restrictions

## BANNED TOOLS — calling these is a skill violation:
- **`EnterPlanMode`** — BANNED. This is a procedural skill. Execute it directly.
- **`ExitPlanMode`** — BANNED. You are never in plan mode.
</tool_restrictions>

# Verify Workflow

Run sequential verification checks. Stop on critical failures.

## Step 0: Detect Tooling

Before running checks, detect the project's tooling:

**Package manager:**
- `pnpm-lock.yaml` → `pnpm`
- `bun.lockb` or `bun.lock` → `bun`
- `yarn.lock` → `yarn`
- `package-lock.json` → `npm`

**Linter:**
- `biome.json` or `biome.jsonc` → `[pm] biome check .`
- `.eslintrc*` or `eslint.config.*` → `[pm] eslint .`

**Test framework:**
- `vitest.config.*` → `[pm] vitest run`
- `jest.config.*` → `[pm] jest`

**Type checker:**
- `tsconfig.json` → `[pm] tsc --noEmit`

**Build:**
- Check `package.json` scripts for `build` → `[pm] run build`

## Step 1: Parse Mode

`$ARGUMENTS` determines the mode:

| Argument | Mode | Checks |
|----------|------|--------|
| `quick` | Quick | Build + types only |
| `full` or (none) | Full | All checks |
| `pre-commit` | Pre-commit | Build + types + lint + debug logs (skip tests) |
| `pre-pr` | Pre-PR | All checks + search for hardcoded secrets |

## Step 2: Run Checks (in order)

Execute each check sequentially. If a check fails critically, report it and stop.

### 2a. Build Check

```bash
[pm] run build
```

- **If FAIL:** Report errors and **STOP** — nothing else matters if it doesn't build
- **If PASS:** Continue

### 2b. Type Check

```bash
[pm] tsc --noEmit
```

- Report error count and locations (`file:line`)
- Continue even if there are errors (report them in summary)

### 2c. Lint Check

```bash
[pm] biome check .
# or
[pm] eslint .
```

- Try auto-fix first: `[pm] biome check --write .`
- Report remaining issues
- Continue

### 2d. Test Suite (skip in `quick` and `pre-commit` modes)

```bash
[pm] vitest run
# or
[pm] jest
```

- Report pass/fail counts
- Report coverage percentage if available
- Continue

### 2e. Debug Log Audit

Search for leftover debug statements in source files (not test files):

**Use Grep tool:** Pattern `console\.(log|debug|dir|table)` in `src/` or `app/` directories, excluding `*.test.*` and `*.spec.*` files

- Report locations
- Note: `console.warn` and `console.error` are intentional — don't flag those

### 2f. Git Status

```bash
git status --short
git diff --stat
```

- Report uncommitted changes count
- Report staged vs unstaged

### 2g. Secrets Scan (pre-pr mode only)

**Use Grep tool:** Search for patterns that suggest hardcoded secrets:
- `sk_live_`, `sk_test_`, `pk_live_`, `pk_test_` (Stripe)
- `AKIA` (AWS access keys)
- `ghp_`, `gho_`, `ghs_` (GitHub tokens)
- `xoxb-`, `xoxp-` (Slack tokens)
- Strings assigned to variables named `*_KEY`, `*_SECRET`, `*_TOKEN` that look like real values (not env var references)

Exclude: `.env*`, `*.example`, `*.md`, `node_modules/`

## Step 3: Summary Report

```
VERIFICATION: [PASS / FAIL]

Build:      [OK / FAIL]
Types:      [OK / X errors]
Lint:       [OK / X issues]
Tests:      [X/Y passed / SKIPPED]
Debug logs: [OK / X found]
Git:        [clean / X uncommitted]
Secrets:    [OK / X found / SKIPPED]

Ready for PR: [YES / NO]
```

If any check failed, list the specific issues below the table with file:line references and brief fix suggestions.

## Edge Cases

- **No build script:** Skip build check, note it in summary
- **No test framework:** Skip tests, note it in summary
- **No TypeScript:** Skip type check, note it in summary
- **Monorepo:** If `turbo.json` or root `pnpm-workspace.yaml` exists, run checks at the workspace level (`turbo build`, `turbo typecheck`)

## What Verify Does NOT Do

- Fix issues (use the fixer agent for that)
- Run E2E tests (use /arc:testing for that)
- Block anything — it reports, the developer decides
- Modify any files

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/howells) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
