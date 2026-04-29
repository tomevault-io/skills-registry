---
name: quality-detect-regressions
description: Compares current quality metrics (tests, coverage, type errors, linting, dead code) to baseline and detects regressions. PROACTIVELY invoked after task completion, before marking task complete, or before merging/committing. Blocks on regressions to prevent quality degradation. Use when completing tasks, validating changes, or checking for quality regressions against baselines. Use when this capability is needed.
metadata:
  author: dawiddutoit
---

# detect-quality-regressions

## Purpose

Compare current quality metrics against a stored baseline to detect regressions in tests, coverage, type errors, linting, and dead code. Enforces quality standards by blocking task completion when metrics degrade beyond tolerance thresholds.

## When to Use

**MANDATORY invocation scenarios:**
- After completing a task (before marking complete)
- Before creating a commit or pull request
- Before merging to main branch
- When user asks "is this done?" or "check quality"

**User trigger phrases:**
- "detect regressions"
- "check against baseline"
- "validate quality hasn't degraded"
- "compare to baseline"

## Quick Start

**Basic usage after completing a task:**

```
1. Complete your code changes
2. Run tests manually to verify they pass
3. Invoke this skill: "Detect regressions against baseline_feature_2025-10-16"
4. If PASS: Mark task complete
5. If FAIL: Fix regressions, re-run detection
```

**This skill automatically:**
- Loads baseline from memory
- Runs ./scripts/check_all.sh
- Compares 5 metrics (tests, coverage, type errors, linting, dead code)
- Detects regressions with tolerance rules
- Returns PASS/FAIL with actionable delta report

## Table of Contents

### Core Sections
- [Instructions](#instructions) - Complete workflow for regression detection
  - [Step 1: Load Baseline from Memory](#step-1-load-baseline-from-memory) - Retrieve and validate baseline metrics
  - [Step 2: Run Current Quality Checks](#step-2-run-current-quality-checks) - Execute check_all.sh and capture metrics
  - [Step 3: Compare Metrics (Regression Detection)](#step-3-compare-metrics-regression-detection) - Apply comparison rules
  - [Step 4: Generate Delta Report](#step-4-generate-delta-report) - Create metric comparison report
  - [Step 5: Return Result](#step-5-return-result) - PASS/FAIL decision logic
- [When to Invoke](#when-to-invoke) - Triggering conditions (after tasks, before commits, status checks)
- [Examples](#examples) - Real-world scenarios
  - [Example 1: No Regressions (PASS)](#example-1-no-regressions-pass) - Successful validation scenario
  - [Example 2: Regression Detected (FAIL)](#example-2-regression-detected-fail) - Handling quality degradation
- [Edge Cases](#edge-cases) - Special situations (missing baseline, check failures, pre-existing issues)

### Advanced Topics
- [Integration Points](#integration-points) - Coordination with other skills and agents
- [Anti-Patterns to Avoid](#anti-patterns-to-avoid) - Common mistakes and correct approaches
- [Success Criteria](#success-criteria) - Validation checklist
- [Supporting Files](#supporting-files) - References and examples
- [Requirements](#requirements) - Environment, memory schema, tools

## Instructions

### Step 1: Load Baseline from Memory

**Query memory for baseline:**

Use `mcp__memory__find_memories_by_name` to retrieve the baseline:

```python
baseline_names = ["baseline_<feature>_<date>"]
# Example: ["baseline_auth_2025-10-16"]
```

**Validate baseline exists:**
- If not found: Return `⚠️ WARNING - No baseline found, suggest capturing baseline first`
- If found: Parse baseline metrics

**Extract baseline metrics:**

Parse the baseline entity's observations to extract:

1. **Tests:** `X passed, Y failed, Z skipped`
2. **Coverage:** `X%`
3. **Type errors:** `X errors`
4. **Linting errors:** `X errors`
5. **Dead code:** `X%`

**Example baseline observations:**
```
- Tests: 145 passed, 0 failed, 3 skipped
- Coverage: 87%
- Type errors: 0
- Linting errors: 0
- Dead code: 1.2%
```

### Step 2: Run Current Quality Checks

**Execute quality checks:**

```bash
cd /Users/dawiddutoit/projects/play/project-watch-mcp
./scripts/check_all.sh
```

**Capture output:**
- Save stdout and stderr
- Parse same 5 metrics as baseline
- Handle script failures (return FAIL if checks can't run)

**Parse current metrics:**

Extract from check_all.sh output:

1. **Tests:** Look for "X passed" in pytest output
2. **Coverage:** Look for "TOTAL" line with percentage
3. **Type errors:** Count errors in pyright output
4. **Linting errors:** Count violations in ruff output
5. **Dead code:** Parse vulture output for percentage

### Step 3: Compare Metrics (Regression Detection)

**Apply comparison rules:**

| Metric        | Rule                              | Tolerance | Regression If             |
|---------------|-----------------------------------|-----------|---------------------------|
| Tests passed  | Must be >= baseline               | None      | current < baseline        |
| Coverage      | Must be >= baseline - 1%          | 1%        | current < baseline - 1%   |
| Type errors   | Must be <= baseline               | None      | current > baseline        |
| Linting       | Must be <= baseline               | None      | current > baseline        |
| Dead code     | Must be <= baseline + 2%          | 2%        | current > baseline + 2%   |

**For each metric:**

1. Calculate change: `current - baseline`
2. Check if regression: Apply rule from table
3. Mark status: `improved`, `stable`, or `regressed`
4. Calculate severity: `critical`, `high`, `medium`, `low`

**Regression severity:**

- **Critical:** Type errors increased (breaks type safety)
- **High:** Tests decreased or coverage dropped >2%
- **Medium:** Linting errors increased
- **Low:** Dead code increased slightly (within tolerance)

### Step 4: Generate Delta Report

**Create comparison for each metric:**

```yaml
metric_name:
  baseline: <value>
  current: <value>
  change: <+/- difference>
  status: improved | stable | regressed
  severity: critical | high | medium | low (if regressed)
```

**Identify regressions:**

Filter metrics where `status == regressed` and create regression list:

```yaml
regressions:
  - metric: tests
    baseline: 152
    current: 150
    change: -2
    severity: high
    action: "Investigate test_user_service.py, test_auth_service.py"
```

**Identify improvements:**

Filter metrics where `status == improved` for positive feedback.

### Step 5: Return Result

**Decision logic:**

```
IF any metric has status == regressed:
  RETURN FAIL with regression list
ELSE:
  RETURN PASS with improvements
```

**PASS result format:**

```
✅ PASS - No regressions detected

Delta Report:
- Tests: +3 passed (148 total) 🎉
- Coverage: +2% (89% total) 🎉
- Type errors: No change (0) ✅
- Linting: No change (0) ✅
- Dead code: -0.1% (1.1% total) 🎉

All metrics maintained or improved. Safe to mark task complete.
```

**FAIL result format:**

```
🔴 FAIL - 4 regressions detected

Regressions:
1. Tests: -2 passed (150 vs 152)
   → 2 tests removed or now failing
   → Action: Investigate test_user_service.py, test_auth_service.py

2. Coverage: -4% (85% vs 89%)
   → Coverage dropped below tolerance (88%)
   → Action: Add tests for newly refactored code

3. Type errors: +2 new errors (5 vs 3)
   → New type errors introduced
   → Action: Run pyright --verbose, fix errors

4. Linting: +2 new errors
   → New linting violations
   → Action: Run ruff check --fix, review changes

❌ BLOCKED - Do not mark task complete until regressions fixed.

Fix order:
1. Fix linting (ruff check --fix)
2. Fix type errors (pyright)
3. Re-run tests (investigate failures)
4. Add coverage for new code
5. Re-run regression detection
```

## When to Invoke

**After completing a task (@implementer, @unit-tester, @integration-tester):**
1. Code changes complete
2. Tests written and pass locally
3. **→ Invoke detect-quality-regressions before marking task complete**
4. If PASS: Mark complete, move to next task
5. If FAIL: Fix regressions, re-run detection

**Before committing/merging:**
1. User requests commit
2. **→ Invoke detect-quality-regressions to validate**
3. If PASS: Proceed with commit
4. If FAIL: Block commit, report regressions

**When checking status (@statuser):**
1. User asks "What's the status?"
2. **→ Invoke detect-quality-regressions to get current quality state**
3. Report status with delta

## Examples

### Example 1: No Regressions (PASS)

**Context:** Completed Task 2.1 (add user authentication)

**Execution:**

```
1. Load baseline: baseline_auth_2025-10-16
   → Tests: 145 passed, 0 failed
   → Coverage: 87%
   → Type errors: 0
   → Linting: 0
   → Dead code: 1.2%

2. Run checks: ./scripts/check_all.sh
   → Tests: 148 passed (+3), 0 failed
   → Coverage: 89% (+2%)
   → Type errors: 0 (no change)
   → Linting: 0 (no change)
   → Dead code: 1.1% (-0.1%)

3. Compare:
   ✅ Tests: 148 >= 145 (PASS)
   ✅ Coverage: 89% >= 86% (87% - 1%) (PASS)
   ✅ Type errors: 0 <= 0 (PASS)
   ✅ Linting: 0 <= 0 (PASS)
   ✅ Dead code: 1.1% <= 3.2% (1.2% + 2%) (PASS)

4. Result: ✅ PASS
```


### Example 2: Regression Detected (FAIL)

**Context:** Completed Task 3.2 (refactor service layer)

**Execution:**

```
1. Load baseline: baseline_service_result_2025-10-16
   → Tests: 152 passed
   → Coverage: 89%
   → Type errors: 3
   → Linting: 0

2. Run checks:
   → Tests: 150 passed (-2) ❌
   → Coverage: 85% (-4%) ❌
   → Type errors: 5 (+2) ❌
   → Linting: 2 (+2) ❌

3. Compare:
   ❌ Tests: 150 < 152 (REGRESSION)
   ❌ Coverage: 85% < 88% (89% - 1%) (REGRESSION)
   ❌ Type errors: 5 > 3 (REGRESSION)
   ❌ Linting: 2 > 0 (REGRESSION)

4. Result: 🔴 FAIL - 4 regressions detected
```

**See [references/regression-fixes.md](./references/regression-fixes.md) for detailed fix strategies**

## Edge Cases

**1. Baseline Not Found**
- Search memory, no baseline exists
- Return: ⚠️ WARNING - No baseline found
- Action: Suggest running capture-quality-baseline first

**2. Quality Checks Fail to Run**
- Script error, tool missing, etc.
- Return: 🔴 FAIL - Unable to validate quality
- Block: Don't allow work to proceed without validation

**3. Pre-Existing Issues in Baseline**
- Baseline has 3 documented type errors
- Current also has 3 type errors (same errors)
- Result: ✅ PASS (no NEW errors)
- Note: "3 pre-existing errors maintained"

**4. Tests Pass But Coverage Drops**
- All tests pass (no failures)
- Coverage dropped 5% (regression)
- Result: 🔴 FAIL (coverage regression)
- Action: Add tests for uncovered code

## Integration Points

**With capture-quality-baseline skill:**
- This skill loads the baseline that capture-quality-baseline created
- Use same baseline naming convention: `baseline_<feature>_<date>`

**With run-quality-gates skill:**
- run-quality-gates ensures quality checks pass (Definition of Done)
- detect-quality-regressions compares against baseline (regression detection)
- Both skills complement each other

**With @implementer:**
- Primary integration - runs after every task
- Blocks task completion if regressions detected

**With @unit-tester / @integration-tester:**
- Runs after writing tests to validate quality didn't degrade

**With manage-todo skill:**
- Task state depends on regression detection result
- Can't mark complete with regressions

## Anti-Patterns to Avoid

❌ **DON'T:** Skip regression detection (always run before task complete)
❌ **DON'T:** Ignore regressions ("I'll fix later")
❌ **DON'T:** Commit without running detection
❌ **DON'T:** Mark task complete with regressions
❌ **DON'T:** Use wrong baseline (old or different feature)

✅ **DO:** Run detection after every task
✅ **DO:** Block on regressions (fail fast)
✅ **DO:** Fix regressions immediately
✅ **DO:** Use correct baseline for feature
✅ **DO:** Document pre-existing issues

## Success Criteria

- ✅ Baseline loaded successfully
- ✅ Quality checks execute
- ✅ All 5 metrics compared correctly
- ✅ Regressions detected accurately
- ✅ Clear PASS/FAIL result
- ✅ Actionable delta report

## Supporting Files

- [references/comparison-rules.md](./references/comparison-rules.md) - Detailed metric comparison rules and tolerances
- [references/regression-fixes.md](./references/regression-fixes.md) - Fix strategies for each metric type

## Requirements

**Environment:**
- Project must be using ./scripts/check_all.sh
- Memory baseline must exist (captured via capture-quality-baseline skill)
- Quality tools must be installed: pyright, ruff, pytest, vulture

**Memory Schema:**
- Entity type: "quality_baseline"
- Entity name: "baseline_<feature>_<date>"
- Observations: List of metric values (see Step 1)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/dawiddutoit) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
