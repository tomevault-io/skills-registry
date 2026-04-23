---
name: legacy-code
description: >- Use when this capability is needed.
metadata:
  author: onesyntax
---

# Legacy Code

Operational guide for incremental improvement of untested legacy systems.

---

## Step 0: Detect Context

Before choosing a strategy, understand what you're working with.

### Test Coverage

Check for any existing tests:

```bash
# Find test files
find . -name "*Test.php" -o -name "*.test.ts" -o -name "*.test.tsx" -o -name "*.spec.ts"

# Check coverage reports
ls -la coverage/ .coverage* 2>/dev/null

# Look for CI config with test runs
cat .github/workflows/* .gitlab-ci.yml .travis.yml 2>/dev/null | grep -E "test|coverage"
```

**What to record:**
- Tests exist: yes/no
- Coverage percentage (if measurable)
- Test type: unit, integration, acceptance, manual
- Last test run date (from CI logs)

### Code Age and Change Frequency

```bash
# See when this code was last modified
git log --format="%ai" -- <file> | head -1

# Count how many times it's been touched
git log --oneline -- <file> | wc -l

# See if it's stable (rare commits) or volatile (frequent commits)
git log --oneline --all -- <file> | head -20
```

**Interpret:**
- **Rarely touched + untested** → Don't improve unless you must touch it (Boy Scout only)
- **Frequently touched + untested** → Highest priority for characterization tests
- **Recently modified + untested** → Likely contains bugs; needs tests before further work
- **Recently modified + tested** → Can refactor more aggressively with test safety net

### Dependency Structure

Check how tightly coupled the code is:

```bash
# Look for imports/requires pointing in and out
grep -r "import.*LegacyModule" .
grep -r "from legacy_module import" .

# Count reverse dependencies
grep -r "require.*legacy" . | wc -l
```

**What matters:**
- Module has well-defined I/O boundaries → Candidate for characterization tests
- Module is deeply coupled to many others → Extract via seam introduction first
- Module is isolated → Easier to wrap with tests

### Reason for Touch

Determine the scope of the change:

- **Bug fix** → Minimal touch required; ideal for one act of kindness
- **New feature** → Consider clean module extraction
- **Required refactoring** → Check if surrounded by tests (strangulation readiness)
- **Performance improvement** → Needs characterization test to verify optimization doesn't break behavior

---

## Step 1: Classify the Situation

Use the decision tree to identify the primary risk and strategy:

### Decision Rules

#### Rule 1: Test Coverage + Touch Frequency

| Situation | Strategy | Urgency |
|-----------|----------|---------|
| Untested + frequently touched | Characterization test first | 🔴 High |
| Untested + rarely touched | Boy Scout only when passing through | 🟢 Low |
| Partially tested + being modified | Add tests for the lines you're changing | 🟡 Medium |
| Well-tested + being modified | Refactor with confidence | 🟢 Low |

#### Rule 2: Feature Addition

| Situation | Strategy |
|-----------|----------|
| Adding feature to untested legacy | Extract to clean module first |
| Adding feature to tested legacy | Can modify in-place with tests |
| Adding feature to system under strangulation | Extend clean boundary, not legacy |

#### Rule 3: Scope and Coupling

| Situation | Action |
|-----------|--------|
| Module is isolated with clear I/O | Write characterization test immediately |
| Module is tightly coupled to 5+ others | Introduce seam first, then characterization test |
| Module is deeply nested with unclear boundaries | Boy Scout acts + refactor-suggestion before testing |

---

## Step 2: Apply Strategy Decision Rules

Choose and execute one of these strategies for the current change.

### Strategy 1: Boy Scout Act of Kindness

**WHEN:** Every single code touch. No exceptions.

**WHEN NOT:** Never skip this.

**Execution:**
1. Make your required change (bug fix, feature, refactoring)
2. Perform exactly ONE act of kindness on the code you touched:
   - Rename a variable or function to reveal intent
   - Split one large function into two smaller ones
   - Extract one duplicated block
   - Remove one dead code block
   - Fix one bad coupling (e.g., remove a global dependency)
   - Add one clarifying comment (only if code can't be self-documenting)
3. Do not attempt multiple acts in one checkin — gently, not aggressively
4. Verify the act doesn't break behavior (if tests exist)
5. Check in with a message: "Boy Scout: [specific act performed]"

**Example (PHP):**
```
Commit message:
  Fix: Customer lookup bug
  Boy Scout: Renamed $c parameter to $customer in searchCustomer()
```

---

### Strategy 2: Characterization Test

**WHEN:** Module has defined input/output boundaries AND you need to modify it safely.

**WHEN NOT:**
- Module is too tightly coupled for isolation
- Behavior is non-deterministic (unless you can normalize it)
- Module has no clear I/O contract

**Execution:**
1. Identify a specific, isolated piece of legacy code (a function, API endpoint, batch processor)
2. Capture its current output given a known input — this is your "golden standard"
3. Save this output as a test file (not in code; use snapshot or fixture)
4. Write a test that calls the legacy code with the known input and verifies the output matches golden standard
5. Run the test — it should pass (you're capturing current behavior, not changing it)
6. Now refactor the internals safely; rerun the test after each change
7. When the test fails, either:
   - You broke something → revert and refactor more carefully
   - Behavior intentionally changed → regenerate the golden standard and update the test
8. As code becomes more testable, migrate from characterization tests to proper unit tests

**Checklist:**
- 🔴 Golden standard captured accurately? (exact output, byte-for-byte if string-based)
- 🔴 Test runs and passes initially? (before any refactoring)
- 🟡 Comparison handles non-deterministic elements? (timestamps, thread IDs normalized)
- 🟡 Test runs fast? (if slow, consider splitting into smaller characterization tests)
- 🟢 Plan to replace with unit tests once code is cleaner?

---

### Strategy 3: Clean Module Extraction

**WHEN:** Adding a new feature to legacy code.

**WHEN NOT:** Modifying existing behavior in legacy code (use Boy Scout + characterization test instead).

**Execution:**
1. Design the new feature as a completely separate, clean module
2. Write the new module using TDD (write test first, implement, refactor)
3. The new module should have:
   - Clear, single responsibility
   - No dependencies on the messy legacy code
   - Full test coverage
   - Clean naming and structure
4. Integrate the new module at a single boundary point in the legacy code
5. Touch the legacy code minimally — ideally one or two lines
6. Apply one Boy Scout act to the legacy code at the integration point
7. Check in the new module and legacy changes together

**Example (PHP):**
```php
// Old way: feature smeared throughout
class LegacyOrderProcessor {
    public function processOrder(Order $order) {
        // ... 300 lines of legacy code ...
        if ($order->customer['loyaltyTier'] === 'GOLD') {
            $points = $order->total * 2;
        }
        // ... 200 more lines ...
    }
}

// New way: clean module extracted
class LoyaltyCalculator {  // new, clean, tested with TDD
    public function calculatePoints(array $customer, float $orderTotal): int {
        $multiplier = $this->loyaltyMultiplier($customer['tier']);
        return (int)($orderTotal * $multiplier);
    }

    private function loyaltyMultiplier(string $tier): float {
        return match ($tier) {
            'GOLD' => 2.0,
            'SILVER' => 1.5,
            default => 1.0,
        };
    }
}

// Integration in legacy code: one line added
class LegacyOrderProcessor {
    private LoyaltyCalculator $calculator;

    public function processOrder(Order $order) {
        // ... legacy code unchanged ...
        $this->calculator->calculatePoints($order->customer, $order->total);  // one line
        // ... legacy code unchanged ...
    }
}
```

**Checklist:**
- 🔴 New module written with TDD (test first)?
- 🔴 New module has no dependencies on legacy code?
- 🔴 Integration point is a single location?
- 🟡 Legacy code touched minimally (1-2 lines)?
- 🟡 One Boy Scout act performed in legacy integration point?
- 🟢 New module has 80%+ test coverage?

---

### Strategy 4: Seam Introduction

**WHEN:** Code is too tightly coupled to be tested, and you can't extract a clean module.

**WHEN NOT:** You have time to do full refactoring.

**Execution:**
1. Identify the hard dependency (global, static method, hard-coded)
2. Introduce a seam — a point where behavior can be substituted (parameter, subclass, dependency injection, interface)
3. Give the seam a default value so existing code doesn't break
4. Pass test double (mock/stub) through the seam in tests
5. Write characterization tests using the seam

**Example (Parameter Seam):**
```php
// Before: can't test without real DB
public function saveOrder(Order $order): void {
    $this->connection->insert('orders', $order->toArray());
}

// After: parameter seam with default
public function saveOrder(Order $order, callable $storage = null): void {
    $storage = $storage ?? fn($o) => $this->connection->insert('orders', $o->toArray());
    $storage($order);
}

// Test can pass fake
$fake = [];
$processor->saveOrder($testOrder, fn($o) => $fake[] = $o);
assert(count($fake) === 1);
```

---

### Strategy 5: Anti-Pattern Guard

**WHEN:** Someone proposes cleanup project, refactoring hunt, or massive rewrite.

**Response template:**
- **Cleanup project:** Decline. "Cleanup projects fail. We improve what we touch, incrementally, via Boy Scout acts."
- **Refactoring hunt:** Decline. "Refactoring untouched code helps nobody. Only improve code you're actively modifying."
- **Massive rewrite:** Decline. "Use strangulation instead — small, surrounded rewrites protected by characterization tests."
- **Feature smeared into legacy:** Redirect. "Extract to clean module, integrate at one boundary point."

---

## Step 3: Execute Change + Act of Kindness

### Execution Checklist

1. **Understand the change** — What must be fixed/added? What tests exist?
2. **Make the required change** — Implement fix/feature; verify existing tests pass
3. **Perform ONE act of kindness** — Rename, split, extract, remove dead code, or fix coupling
4. **Verify behavior unchanged** — Tests or manual verification; gently, not aggressively
5. **Check in** — Commit message: `[Action]: [What]. Boy Scout: [specific act]`

---

## Step 4: Assess Strangulation Readiness

After each change, check if clean code now surrounds untested legacy code.

### Assessment Questions

1. **Is there untested legacy code that hasn't been modified in months?**
   - Yes → Go to question 2
   - No → No strangulation opportunity yet

2. **Is that legacy code now surrounded by clean, tested modules on multiple sides?**
   - Yes → Go to question 3
   - No → Continue incrementally improving the surrounding code

3. **Can I rewrite this surrounded legacy code as a small, isolated unit?**
   - Yes → Strangulation is ready. Plan a rewrite protected by tests.
   - No → Identify which surrounding parts still need to be clean. Continue incrementally.

### Strangulation Readiness Progression

**Phase 1 (Legacy Everywhere):** No safety net. Improve volatile code touched frequently first.
**Phase 2 (Clean Modules Growing):** Making progress. Keep surrounding untested code with tested modules.
**Phase 3 (Legacy Surrounded):** READY. Safe to rewrite isolated legacy module with characterization test protection.

### After Strangulation Identification

If you identify surrounded legacy code:
1. Do not rewrite it immediately
2. Write characterization test for the surrounded code
3. Plan a small, focused rewrite (not a big project)
4. Execute the rewrite in small pieces, protected by characterization test
5. After rewrite, replace characterization test with proper unit tests

---

## K-Line History

Lessons from applying legacy code techniques.

### ✓ What Works

- **Boy Scout acts compound:** Small improvements accumulate month after month; codebase noticeably cleaner over time.
- **Incremental beats big projects:** Teams improving incrementally succeed. Teams requesting "cleanup time" fail.
- **Characterization tests enable confidence:** Once you can run code and verify output, refactoring speed increases dramatically.

### ✗ What Fails

- **Cleanup projects:** Always fail; teams lose morale and code gets worse.
- **Refactoring hunts:** Waste effort on untouched code that will never be modified.
- **Massive rewrites:** New system accumulates its own problems while old system evolves.

---

## When NOT to Apply

| Situation | Alternative |
|-----------|-------------|
| Isolated module, never touched, untested | Boy Scout only when you must touch it |
| Brand new greenfield feature | Use `/tdd` and `/solid` normally |
| Code already well-tested and clean | Use standard refactoring |
| Simple three-line bug fix | Boy Scout: one rename, done |
| Full system rewrite planned | Plan strangulation path instead |

---

## Decision Tree Quick Reference

```
You need to touch legacy code.
│
├─ Is there an existing test?
│  ├─ YES → Refactor with confidence using /refactor-suggestion
│  └─ NO → Go next
│
├─ Is this a new feature?
│  ├─ YES → Extract clean module, integrate at boundary
│  └─ NO → Go next
│
├─ Does the code have clear I/O boundaries?
│  ├─ YES → Write characterization test first
│  └─ NO → Go next
│
├─ Is the code tightly coupled?
│  ├─ YES → Introduce seam, then characterization test
│  └─ NO → Go next
│
└─ Make your required change + Boy Scout act
   └─ Check in, assess strangulation readiness
```

---

## Related Skills

- **/tdd** — Use for new features extracted from legacy code
- **/refactor-suggestion** — Identify specific code smells and refactoring techniques
- **/solid** — Apply SOLID principles to new clean modules
- **/architecture** — Design clean module boundaries for extraction
- **/functions** — Break large legacy functions into smaller ones (Boy Scout)
- **/naming** — Rename legacy variables and functions (Boy Scout)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/onesyntax) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-12 -->
