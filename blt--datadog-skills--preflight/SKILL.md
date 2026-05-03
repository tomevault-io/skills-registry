---
name: preflight
description: Environment validation checklist. Run this FIRST when starting a new Claude session to verify the environment is ready for optimization work. Checks build tools, test runners, benchstat, git, and database access. Use when this capability is needed.
metadata:
  author: blt
---

# Pre-flight Checklist

**Run this skill FIRST when starting a new session.**

This validates that the environment is properly configured for optimization hunting, reviewing, and rescuing.

---

## Execute All Checks

Run each check in order. **STOP on any failure and report the issue.**

### 1. Build Tools

```bash
echo "=== Check 1: Build Tools ==="
dda --version
```
**Expected:** Version string like `dda version X.Y.Z`
**If fails:** Not in dev container, or dda not in PATH

```bash
go version
```
**Expected:** `go version go1.2X.X linux/amd64`
**If fails:** Go not installed or not in PATH

---

### 2. Can Build Code

```bash
echo "=== Check 2: Build Test ==="
go build ./pkg/metrics/...
```
**Expected:** No output (success)
**If fails:** Run `go mod download`, check modules.yml

---

### 3. Can Run Tests

```bash
echo "=== Check 3: Test Runner ==="
go test -short -count=1 ./pkg/aggregator/ckey/... 2>&1
```
**Expected:** `ok` lines for each package
**If fails:** Check test dependencies

Note: Use `pkg/aggregator/ckey` for preflight - it has minimal dependencies.
Some packages (like `pkg/metrics`) may fail due to config assertions in test setup.

---

### 4. Benchstat Available

```bash
echo "=== Check 4: Benchstat ==="
which benchstat || (echo "Installing benchstat..." && go install golang.org/x/perf/cmd/benchstat@latest)
benchstat --help 2>&1 | head -1
```
**Expected:** Usage information
**If fails:** `go install golang.org/x/perf/cmd/benchstat@latest`

---

### 5. Git Configured

```bash
echo "=== Check 5: Git ==="
git status | head -3
git config user.name
git config user.email
```
**Expected:** Branch info, name, and email
**If fails:** Configure with `git config user.name "Name"` and `git config user.email "email"`

---

### 6. Skills Databases Writable

```bash
echo "=== Check 6: Database Access ==="
SKILLS=".claude/skills"
touch "$SKILLS/hunt-optimization/hunts.yaml" && echo "✓ hunts.yaml writable"
touch "$SKILLS/review-optimization/reviews.yaml" && echo "✓ reviews.yaml writable"
touch "$SKILLS/rescue-optimization/rescues.yaml" && echo "✓ rescues.yaml writable"
touch "$SKILLS/validate-correctness/validations.yaml" && echo "✓ validations.yaml writable"
```
**Expected:** All 4 databases writable
**If fails:** Check permissions, may need `chmod 644` or ownership fix

---

### 7. Skills Readable

```bash
echo "=== Check 7: Skills Present ==="
head -2 .claude/skills/hunt-optimization/SKILL.md | grep -q "^name:" && echo "✓ hunt-optimization"
head -2 .claude/skills/review-optimization/SKILL.md | grep -q "^name:" && echo "✓ review-optimization"
head -2 .claude/skills/rescue-optimization/SKILL.md | grep -q "^name:" && echo "✓ rescue-optimization"
head -2 .claude/skills/validate-correctness/SKILL.md | grep -q "^name:" && echo "✓ validate-correctness"
```
**Expected:** All 4 skills present
**If fails:** Skills not mounted/installed correctly

---

## Quick All-in-One Check

```bash
#!/bin/bash
set -e  # Exit on ANY error

echo "=== PREFLIGHT START ==="

echo "Check 1: dda..."
dda --version > /dev/null

echo "Check 2: go..."
go version > /dev/null

echo "Check 3: build..."
go build ./pkg/aggregator/ckey/...

echo "Check 4: test..."
go test -short -count=1 ./pkg/aggregator/ckey/... > /dev/null

echo "Check 5: benchstat..."
which benchstat > /dev/null

echo "Check 6: git..."
git config user.name > /dev/null
git config user.email > /dev/null

echo "Check 7: databases writable..."
touch .claude/skills/hunt-optimization/hunts.yaml
touch .claude/skills/review-optimization/reviews.yaml
touch .claude/skills/rescue-optimization/rescues.yaml
touch .claude/skills/validate-correctness/validations.yaml

echo "Check 8: skills readable..."
head -1 .claude/skills/hunt-optimization/SKILL.md > /dev/null
head -1 .claude/skills/review-optimization/SKILL.md > /dev/null
head -1 .claude/skills/rescue-optimization/SKILL.md > /dev/null
head -1 .claude/skills/validate-correctness/SKILL.md > /dev/null
head -1 .claude/skills/preflight/SKILL.md > /dev/null

echo "=== PREFLIGHT PASSED ==="
```

**CRITICAL: The `set -e` ensures the script exits immediately on ANY failure.**
If you see output stop before "PREFLIGHT PASSED", a check failed.

---

## Report Format

After running checks, output:

```
PREFLIGHT REPORT
================
Timestamp: <current time>
Status: PASS | FAIL

Checks:
  [✓] dda --version
  [✓] go version
  [✓] go build
  [✓] go test
  [✓] benchstat
  [✓] git config
  [✓] databases writable
  [✓] skills readable

Ready for: /hunt-optimization, /review-optimization, /rescue-optimization
```

Or if failed:

```
PREFLIGHT REPORT
================
Status: FAIL

Failed checks:
  [✗] benchstat - not found
      Fix: go install golang.org/x/perf/cmd/benchstat@latest

  [✗] databases writable - permission denied
      Fix: Check .claude/skills mount permissions

DO NOT proceed with optimization skills until all checks pass.
```

---

## Usage

```
/preflight                    # Run full checklist
```

**Run this at the start of every new Claude session before any optimization work.**

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/blt) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
