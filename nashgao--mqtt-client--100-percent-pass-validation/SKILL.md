---
name: 100-percent-pass-validation
description: Mandatory 100% pass rate validation for all test commands Use when this capability is needed.
metadata:
  author: nashgao
---

# 🚨 MANDATORY 100% PASS RATE VALIDATION

**CRITICAL: This MUST be integrated into ALL test commands to prevent false success claims**

## 🚨 ZERO TOLERANCE ENFORCEMENT

**MANDATORY - This validation enforces PERFECT test execution across ALL test commands:**

### Universal Success Criteria (ALL must be met)
- ✅ **0 Failed Tests** - Every single test must pass (not 99%, not "mostly")
- ✅ **0 Errors** - No runtime errors allowed in any test
- ✅ **0 Warnings** - Warnings are treated as hard failures
- ✅ **0 Deprecations** - Deprecation notices block success
- ✅ **0 Incomplete Tests** - Incomplete tests counted as failures
- ✅ **0 Risky Tests** - Risky test detection must pass
- ✅ **0 Skipped Tests** - Unless explicitly allowed with justification
- ✅ **100% Verified** - Triple validation confirms consistency

### Failure Response Protocol
When ANY issue is detected:
1. **STOP** - Do not proceed or claim success
2. **REPORT** - List exact issues with counts and file:line references
3. **FIX** - Resolve ALL issues before re-testing
4. **VERIFY** - Run triple validation to confirm 100% pass rate
5. **DOCUMENT** - Provide evidence logs for all validation runs

### Exit Codes (Enforced Across All Test Commands)
- `0` = Perfect execution (no warnings, no deprecations, no failures)
- `1` = Any failure, warning, deprecation, or incomplete test
- `2` = Configuration or setup error

---

## ⛔ THE PROBLEM WE'RE SOLVING

Test agents were claiming success without verification:
- Running subset of tests and claiming "all fixed"
- Accepting exit code 0 without checking for actual test execution
- Declaring victory after fixing "some" tests
- Not validating that 100% of tests actually pass

## 🔒 MANDATORY VALIDATION PATTERN

### For Every Test Command

```bash
#!/bin/bash

# 🚨 CRITICAL: 100% PASS RATE ENFORCEMENT
validate_100_percent_pass_rate() {
    local test_command="$1"
    local validation_log="$2"

    echo "=== 🔒 100% PASS RATE VALIDATION ==="

    # Step 1: Run FULL test suite (not subset)
    echo "Step 1/5: Executing FULL test suite..."
    $test_command 2>&1 | tee "$validation_log"
    local exit_code=$?

    # Step 2: Verify exit code is 0
    if [ $exit_code -ne 0 ]; then
        echo "❌ VALIDATION FAILED: Exit code $exit_code (expected 0)"
        echo "📊 Status: TESTS FAILING - NOT 100% PASS RATE"
        return 1
    fi
    echo "✅ Step 2/5: Exit code 0 verified"

    # Step 3: Verify test output exists
    if [ ! -s "$validation_log" ]; then
        echo "❌ VALIDATION FAILED: No test output captured"
        echo "📊 Status: TESTS MAY NOT HAVE RUN - CANNOT VERIFY 100%"
        return 1
    fi
    echo "✅ Step 3/5: Test output captured"

    # Step 4: Check for positive success indicators
    if ! grep -E "(Tests:.*[0-9]+.*passed|✓.*All tests passed|PASSED.*100%|OK \([0-9]+ tests?\))" "$validation_log" > /dev/null; then
        echo "❌ VALIDATION FAILED: No positive success indicators"
        echo "📊 Status: CANNOT CONFIRM 100% PASS RATE"
        return 1
    fi
    echo "✅ Step 4/5: Positive success indicators found"

    # Step 5: Verify ZERO failures
    if grep -E "(FAIL|FAILED|ERROR|✗|✖|[1-9][0-9]* failing)" "$validation_log" > /dev/null; then
        echo "❌ VALIDATION FAILED: Failure patterns detected"
        echo "📊 Status: TESTS STILL FAILING - NOT 100% PASS RATE"
        return 1
    fi
    echo "✅ Step 5/5: Zero failures confirmed"

    # Extract and report exact numbers
    extract_test_counts "$validation_log"

    echo "🎯 100% PASS RATE VALIDATED SUCCESSFULLY"
    return 0
}

# Extract exact test counts for reporting
extract_test_counts() {
    local log_file="$1"

    echo "📊 TEST RESULTS SUMMARY:"

    # PHPUnit pattern
    if grep -q "OK ([0-9]* test" "$log_file"; then
        local count=$(grep -o "OK ([0-9]* test" "$log_file" | grep -o "[0-9]*")
        echo "  Total Tests: $count"
        echo "  Passed: $count (100%)"
        echo "  Failed: 0"
        return
    fi

    # Jest/Vitest pattern
    if grep -q "Tests:.*[0-9]* passed" "$log_file"; then
        local passed=$(grep -o "[0-9]* passed" "$log_file" | head -1 | grep -o "[0-9]*")
        local total=$(grep -o "[0-9]* total" "$log_file" | head -1 | grep -o "[0-9]*")
        echo "  Total Tests: $total"
        echo "  Passed: $passed ($(( passed * 100 / total ))%)"
        echo "  Failed: $(( total - passed ))"
        return
    fi

    # Pytest pattern
    if grep -q "passed" "$log_file" && grep -q "=.*passed.*in.*seconds.*=" "$log_file"; then
        local passed=$(grep -o "[0-9]* passed" "$log_file" | head -1 | grep -o "[0-9]*")
        echo "  Total Tests: $passed"
        echo "  Passed: $passed (100%)"
        echo "  Failed: 0"
        return
    fi

    # Go test pattern
    if grep -q "PASS" "$log_file" && grep -q "ok.*coverage" "$log_file"; then
        local test_count=$(grep -c "PASS" "$log_file")
        echo "  Test Packages: $test_count"
        echo "  Status: ALL PASSING (100%)"
        return
    fi

    # Generic fallback
    echo "  Status: All tests passing (framework-specific counts not extracted)"
}
```

## 🔄 TRIPLE VALIDATION REQUIREMENT

### Mandatory for Final Success Claims

```bash
# 🚨 CRITICAL: TRIPLE VALIDATION FOR 100% CONFIDENCE
triple_validation_100_percent() {
    local test_command="$1"
    local pass_count=0
    local required_passes=3

    echo "=== 🔒 TRIPLE VALIDATION FOR 100% PASS RATE ==="
    echo "Requirement: $required_passes consecutive successful runs"

    for i in 1 2 3; do
        echo ""
        echo "🔍 Validation Run $i/$required_passes"
        echo "─────────────────────────────"

        if validate_100_percent_pass_rate "$test_command" "validation_run_${i}.log"; then
            pass_count=$((pass_count + 1))
            echo "✅ Run $i: PASSED (100% pass rate confirmed)"
        else
            echo "❌ Run $i: FAILED (not 100% pass rate)"
            echo "🔄 Triple validation failed at run $i"
            echo "📊 Final Status: Only $pass_count/$required_passes validations passed"
            echo "⚠️  CANNOT CONFIRM 100% PASS RATE"
            return 1
        fi
    done

    echo ""
    echo "🎉 TRIPLE VALIDATION COMPLETE"
    echo "📊 Final Status: $pass_count/$required_passes validations passed"
    echo "✅ 100% PASS RATE CONFIRMED WITH HIGH CONFIDENCE"
    return 0
}
```

## 📋 INTEGRATION CHECKLIST

### For Test Commands

```markdown
## ✅ 100% Pass Rate Validation Checklist

Before claiming ANY test success:

□ **Full Suite**: Ran complete test suite (not cherry-picked)
□ **Exit Code 0**: Test command returned success
□ **Output Exists**: Test output captured and non-empty
□ **Positive Indicators**: Found "PASSED", "✓", or "OK" patterns
□ **Zero Failures**: No "FAIL", "ERROR", or "✗" patterns
□ **Exact Counts**: Can report exact pass/fail numbers
□ **Triple Validation**: Passed 3 consecutive runs
□ **100% Confirmed**: All tests verified passing

❌ **IF ANY UNCHECKED**: Cannot claim 100% pass rate
```

## 🚫 FORBIDDEN CLAIMS WITHOUT VALIDATION

```markdown
❌ **NEVER SAY THESE WITHOUT VALIDATION:**
- "All tests fixed" (without running full suite)
- "100% pass rate achieved" (without triple validation)
- "Tests are passing" (without exact counts)
- "Should work now" (without verification)
- "Fixed the failures" (without confirming zero remain)
```

## 📊 REPORTING TEMPLATE

### After Successful 100% Validation

```markdown
## ✅ 100% TEST PASS RATE ACHIEVED

**Validation Summary:**
- Command: `[exact command used]`
- Total Tests: [exact count]
- Passed: [exact count] (100%)
- Failed: 0
- Skipped: 0

**Verification Method:**
- ✅ Full test suite executed
- ✅ Exit code 0 confirmed
- ✅ Positive indicators found
- ✅ Zero failure patterns
- ✅ Triple validation passed (3/3)

**Evidence:**
- Run 1: [log file] - 100% pass
- Run 2: [log file] - 100% pass
- Run 3: [log file] - 100% pass

**Confidence Level:** HIGH (triple-validated)
```

## 🔴 ENFORCEMENT CONSEQUENCES

### What Happens Without 100% Validation

1. **Premature Success Claim** → Task marked FAILED
2. **Partial Testing** → Results invalidated
3. **Missing Validation** → Must restart from beginning
4. **False 100% Claim** → Credibility loss
5. **Incomplete Verification** → User trust damaged

## 💻 USAGE EXAMPLES

### In Test Fix Commands

```bash
# Example: PHP test fixing with 100% validation
fix_php_tests() {
    echo "🔧 Fixing PHP tests..."

    # ... fix implementation ...

    # MANDATORY: Validate 100% pass rate
    echo "🔍 Validating 100% pass rate..."
    if ! triple_validation_100_percent "composer test:integration"; then
        echo "❌ FAILED: Could not achieve 100% pass rate"
        return 1
    fi

    echo "✅ SUCCESS: 100% pass rate achieved and validated!"
}

# Example: JavaScript test fixing with 100% validation
fix_js_tests() {
    echo "🔧 Fixing JavaScript tests..."

    # ... fix implementation ...

    # MANDATORY: Validate 100% pass rate
    echo "🔍 Validating 100% pass rate..."
    if ! triple_validation_100_percent "npm test"; then
        echo "❌ FAILED: Could not achieve 100% pass rate"
        return 1
    fi

    echo "✅ SUCCESS: 100% pass rate achieved and validated!"
}
```

## 🎯 KEY PRINCIPLES

1. **No Assumptions**: Never assume tests pass without running them
2. **Full Coverage**: Always run complete test suite, not subsets
3. **Multiple Signals**: Exit code alone is insufficient
4. **Exact Counts**: Must report specific numbers, not vague claims
5. **Repeatability**: Success must be consistent across multiple runs

---

**REMEMBER: 100% means 100% - not 99%, not "mostly", not "should be". VALIDATE RUTHLESSLY.**

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/nashgao) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
