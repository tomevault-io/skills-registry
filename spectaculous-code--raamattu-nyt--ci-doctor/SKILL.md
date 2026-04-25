---
name: ci-doctor
description: | Use when this capability is needed.
metadata:
  author: spectaculous-code
---

# CI Doctor

Diagnose and fix CI failures fast. Read logs, identify root cause, fix or delegate.

## Context Files (Read First)

For project structure, read from `Docs/context/`:
- `Docs/context/repo-structure.md` - File locations
- `Docs/context/conventions.md` - CI/build patterns

## Workflow

```
1. FETCH    → Get failure details (gh cli or logs)
2. DIAGNOSE → Identify error category
3. FIX      → Apply fix or delegate to specialist skill
4. VERIFY   → Run locally to confirm
5. PUSH     → Commit and push fix
```

## Step 1: Fetch Failure Details

```bash
# Get recent workflow runs
gh run list --limit 5

# Get failed run details
gh run view <run-id>

# Get job logs
gh run view <run-id> --log-failed

# Get PR checks
gh pr checks
```

If user provides a GitHub URL, extract info with `gh`:
```bash
gh pr view <url> --json statusCheckRollup
gh run view <url>
```

## Step 2: Diagnose Error Category

| Error Pattern | Category | Action |
|---------------|----------|--------|
| `tsc` errors, "TS2xxx" | TypeScript | Fix type errors |
| `biome lint`, "lint error" | Lint | Delegate to `lint-fixer` |
| `FAIL src/...test.ts` | Test failure | Delegate to `test-writer` or fix |
| `npm ci` failed | Dependency | Check package-lock.json |
| `types.ts changed` | Generated types | Regenerate and commit |
| `timeout`, `ETIMEDOUT` | Flaky/infra | Retry or increase timeout |
| `permission denied` | Secrets/auth | Check workflow permissions |
| CodeQL alert, code scanning | Security vulnerability | See "Code Scanning Alerts" section |

## Step 3: Fix by Category

### TypeScript Errors

```bash
# Run locally to see all errors
npm run typecheck:ci
# or
npx tsc -p tsconfig.ci.json --noEmit
```

Fix each error. Common patterns:
- Missing imports
- Type mismatches
- Unused variables — **CAUTION:** See "Unused Variable Safety" section below

### Lint Errors

Delegate: `Use the lint-fixer skill`

Or quick fix:
```bash
npx @biomejs/biome check --write .
```

### Test Failures

1. Run failing test locally:
```bash
npm run test --workspace=apps/raamattu-nyt -- --run <test-file>
```

2. If complex, delegate: `Use the systematic-debugging skill`

3. If test needs update, delegate: `Use the test-writer skill`

### Generated Types Out of Sync

**Supabase types:**
```bash
# Regenerate (requires SUPABASE_PROJECT_ID and SUPABASE_ACCESS_TOKEN)
npx supabase gen types typescript --project-id "$SUPABASE_PROJECT_ID" > apps/raamattu-nyt/src/integrations/supabase/types.ts
git add apps/raamattu-nyt/src/integrations/supabase/types.ts
git commit -m "Regenerate Supabase types"
```

**OpenAPI types:**
```bash
npx openapi-typescript ./openapi.yaml -o apps/raamattu-nyt/src/lib/openapi.types.ts
git add apps/raamattu-nyt/src/lib/openapi.types.ts
git commit -m "Regenerate OpenAPI types"
```

### Dependency Issues

```bash
# Clear and reinstall
rm -rf node_modules package-lock.json
npm install
git add package-lock.json
git commit -m "Refresh package-lock.json"
```

### Code Scanning Alerts (CodeQL)

GitHub Code Scanning uses CodeQL to find security vulnerabilities.

#### Step 1: Fetch and Summarize

```bash
# Summary by rule type (always start here)
gh api 'repos/{owner}/{repo}/code-scanning/alerts?state=open&per_page=100' \
  --jq '[.[] | .rule.id] | group_by(.) | map({rule: .[0], count: length}) | sort_by(-.count)'

# Summary by file
gh api 'repos/{owner}/{repo}/code-scanning/alerts?state=open&per_page=100' \
  --jq '[.[] | .most_recent_instance.location.path] | group_by(.) | map({file: .[0], count: length}) | sort_by(-.count)'

# Filter to actual security issues (skip noise)
gh api 'repos/{owner}/{repo}/code-scanning/alerts?state=open&per_page=100' \
  --jq '[.[] | select(.rule.id | test("syntax-error|unused-local") | not)] | map({number, rule: .rule.id, severity: .rule.security_severity_level, file: .most_recent_instance.location.path})'

# Get specific alert details
gh api repos/{owner}/{repo}/code-scanning/alerts/<alert-number>
```

#### Step 2: TRIAGE FIRST (CRITICAL)

**Before fixing anything, categorize each alert.** Many CodeQL alerts are false positives in this codebase.

| Alert Rule | Likely Action | Why |
|------------|---------------|-----|
| `js/syntax-error` on `*-types.ts` | **DISMISS** as false positive | Auto-generated Supabase type files, not real syntax errors |
| `js/unused-local-variable` | **VERIFY INDIVIDUALLY** | See "Unused Variable Safety" section — CodeQL misses JSX usage |
| `js/incomplete-*-sanitization` on `*.test.ts` | **DISMISS** as test code | Test files intentionally test sanitization edge cases |
| `js/bad-tag-filter` on `*.test.ts` | **DISMISS** as test code | Test files intentionally test regex filters |
| `js/xss-through-dom` | **INVESTIGATE** | May be real — read the code and check if user input reaches innerHTML |
| `js/incomplete-url-substring-sanitization` | **INVESTIGATE** | May be real — check if URL validation is actually vulnerable |
| `js/useless-assignment-to-local` | **INVESTIGATE** | Low priority, cosmetic — check if assignment has side effects |

**Known false positive patterns in this project:**
- **Generated type files** (`types.ts`, `bible-schema-types.ts`, `notifications-schema-types.ts`) — CodeQL cannot parse Supabase's generated TypeScript. Dismiss all `js/syntax-error` alerts on these files.
- **Test files** (`*.test.ts`, `*.test.tsx`) — Tests intentionally create insecure patterns to verify sanitization works. Dismiss sanitization alerts on test files.
- **JSX icon/component imports** — CodeQL flags React component imports as "unused variables" because it doesn't understand `<Component />` JSX syntax. See "Unused Variable Safety" section.

#### Step 3: Handle Each Category

**For real security alerts** (`js/xss`, `js/sql-injection`, `js/url-*`):
1. Read the affected file and understand the data flow
2. Verify untrusted user input actually reaches the vulnerable sink
3. Apply the appropriate fix (see table below)
4. Test locally
5. Commit and push — CodeQL will re-analyze

**For false positives:**
```bash
# Dismiss as false positive (generated code)
gh api -X PATCH 'repos/{owner}/{repo}/code-scanning/alerts/<number>' \
  -f state=dismissed -f 'dismissed_reason=false positive' \
  -f dismissed_comment="Auto-generated Supabase type file"

# Dismiss as test code
gh api -X PATCH 'repos/{owner}/{repo}/code-scanning/alerts/<number>' \
  -f state=dismissed -f 'dismissed_reason=false positive' \
  -f dismissed_comment="Test file intentionally testing sanitization"

# Dismiss unused-local-variable after verifying JSX usage
gh api -X PATCH 'repos/{owner}/{repo}/code-scanning/alerts/<number>' \
  -f state=dismissed -f 'dismissed_reason=false positive' \
  -f dismissed_comment="Import used as JSX component: <ComponentName />"
```

**For `js/unused-local-variable`:** See "Unused Variable Safety" section below. NEVER bulk-remove.

**Common real security fixes:**

| Alert Type | Fix |
|------------|-----|
| `js/xss` / `js/xss-through-dom` | Use `textContent` not `innerHTML`, or sanitize with DOMPurify |
| `js/sql-injection` | Use parameterized queries, never concatenate user input |
| `js/path-injection` | Validate/sanitize file paths, use `path.join` with basename |
| `js/incomplete-url-substring-sanitization` | Use `new URL()` parsing instead of substring checks |
| `js/prototype-pollution` | Use `Object.create(null)` or validate object keys |
| `js/insecure-randomness` | Use `crypto.randomUUID()` instead of `Math.random()` |
| `js/hardcoded-credentials` | Move secrets to environment variables |

#### Step 4: Batch-dismiss Known False Positives

For bulk dismissal of generated file alerts, process them in a loop:
```bash
# Get all syntax-error alerts on generated type files
gh api 'repos/{owner}/{repo}/code-scanning/alerts?state=open&per_page=100' \
  --jq '.[] | select(.rule.id == "js/syntax-error") | .number' | while read num; do
  gh api -X PATCH "repos/{owner}/{repo}/code-scanning/alerts/$num" \
    -f state=dismissed -f 'dismissed_reason=false positive' \
    -f dismissed_comment="Auto-generated Supabase type file"
done
```

### Unused Variable Safety (CRITICAL)

**CodeQL `js/unused-local-variable` alerts are frequently WRONG for React/JSX codebases.**

CodeQL's static analysis cannot detect:
- JSX component usage: `<Heart className="..." />` — `Heart` looks "unused" as a JS variable
- Destructured props used in JSX: `const { limit } = usage` → `{limit.toLocaleString()}`
- Hook calls: `const { toast } = useToast()` → `toast(...)` in event handlers

**NEVER bulk-remove CodeQL unused variable alerts.** For each flagged variable:

```bash
# 1. Grep for ALL usages including JSX
grep -n "variableName" path/to/file.tsx

# 2. Specifically check JSX usage (components/icons)
grep -n "<variableName" path/to/file.tsx

# 3. Check if it's used in template literals
grep -n "{variableName" path/to/file.tsx
```

**Only safe to remove/rename:**
- Imports with genuinely zero references in the file
- Catch parameters (`catch (err)` → `catch (_err)`) IF `err` is not used in catch block
- Function parameters that are truly unused (and renaming won't break callers)

**Never rename with `_` prefix if the original name is used anywhere in the file.**

**Incident reference:** Commit `df849425` bulk-removed "unused" variables across 24 files. 14 were actually in use, causing runtime crashes. Required 8 fix commits to repair.

## Step 4: Verify Locally

Before pushing, run the same checks CI runs:

```bash
# TypeScript
npm run typecheck:ci || npx tsc -p tsconfig.ci.json

# Lint
npx @biomejs/biome lint .

# Build
npm run build

# Tests
npm run test --workspace=apps/raamattu-nyt
```

## Step 5: Push Fix

```bash
git add -A
git commit -m "Fix CI: <brief description>"
git push
```

Then monitor: `gh run watch`

## Project CI Structure

This project has these workflows:

| Workflow | File | Triggers | Checks |
|----------|------|----------|--------|
| CI | `.github/workflows/ci.yml` | PR, push to main | TypeScript, Lint, Build |
| Tests | `.github/workflows/tests.yml` | PR, push (code changes) | Vitest, Playwright smoke |
| Supabase Sync | `.github/workflows/supabase-sync.yml` | Various | Type generation |

## Delegation Guide

| Situation | Delegate To |
|-----------|-------------|
| Lint/format errors | `lint-fixer` skill |
| Test needs rewriting | `test-writer` skill |
| Complex bug in test | `systematic-debugging` skill |
| Supabase migration issue | `supabase-migration-writer` skill |
| Type refactoring needed | `code-refactoring` skill |
| RLS/GRANT security gaps | `security-auditor` skill |
| Auth flow vulnerabilities | `auth-shield` skill |

## Quick Commands Reference

```bash
# See what's failing
gh pr checks
gh run list --limit 3

# Get logs for failed run
gh run view <id> --log-failed

# Re-run failed jobs
gh run rerun <id> --failed

# Watch current run
gh run watch
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/spectaculous-code) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
