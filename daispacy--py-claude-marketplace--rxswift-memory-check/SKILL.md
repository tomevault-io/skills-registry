---
name: rxswift-memory-check
description: Quick RxSwift memory leak detection for iOS. Finds missing dispose bags, retain cycles, and strong self references. Use when debugging memory issues, checking Observable subscriptions, or investigating retain cycles in RxSwift code. Use when this capability is needed.
metadata:
  author: daispacy
---

# RxSwift Memory Leak Detector

Fast, focused check for RxSwift memory management issues in iOS code.

## When to Activate

- "memory leak", "retain cycle", "dispose bag"
- "RxSwift memory issues", "check subscriptions"
- "[weak self]", "memory management"
- Debugging memory problems or crashes

## Quick Check Process

### Step 1: Find RxSwift Subscriptions

Use Grep to locate all Observable subscriptions:
- Pattern: `\.subscribe\(`
- Check surrounding code for proper disposal

### Step 2: Verify Each Subscription

For every `.subscribe(`:
1. ✅ Has `.disposed(by: disposeBag)`
2. ✅ Uses `[weak self]` or `[unowned self]` in closures
3. ✅ DisposeBag is a property, not local variable

### Step 3: Check Common Patterns

#### 🔴 Pattern 1: Missing Disposal
```swift
viewModel.data
    .subscribe(onNext: { data in })
    // MISSING: .disposed(by: disposeBag)
```

#### 🔴 Pattern 2: Retain Cycle
```swift
viewModel.data
    .subscribe(onNext: { data in
        self.updateUI(data)  // Strong self!
    })
```

#### 🔴 Pattern 3: Local DisposeBag
```swift
func loadData() {
    let disposeBag = DisposeBag()  // Local variable!
    // Cancels immediately when function ends
}
```

### Step 4: Generate Report

Focused report with:
- Critical issues by severity
- File locations and line numbers
- Current vs. fixed code
- Impact assessment
- Recommended fixes

## Search Patterns

### Find subscriptions without disposal
```
Pattern: \.subscribe\(
Context: Check next 5 lines for .disposed
```

### Find strong self references
```
Pattern: subscribe.*\{[^[]*self\.
Context: -A 3 -B 1
```

### Find local DisposeBag declarations
```
Pattern: let disposeBag = DisposeBag\(\)
```

## Output Format

```markdown
# RxSwift Memory Check Report

## Critical Issues: X

### 1. Missing Disposal - MEMORY LEAK
**File**: `PaymentViewModel.swift:45`
**Risk**: Memory accumulation, eventual crash

**Current**:
```swift
// Missing disposal
```

**Fix**:
```swift
.disposed(by: disposeBag)
```

**Impact**: [Explanation]

---

## Summary
🔴 Critical: X (memory leaks/retain cycles)
⚠️  Warnings: X (could use weak self)

## Files Status
✅ Clean files
⚠️  Files with warnings
🔴 Files with critical issues
```

## DisposeBag Best Practices

✅ **Correct**: Property-level DisposeBag
```swift
class ViewModel {
    private let disposeBag = DisposeBag()
}
```

❌ **Wrong**: Local DisposeBag
```swift
func loadData() {
    let disposeBag = DisposeBag()  // Cancels immediately!
}
```

## When to Use `weak` vs `unowned`

- **Default**: Always use `[weak self]` (safer)
- **Rare**: Use `[unowned self]` only if 100% sure self outlives subscription

## Quick Fix Guide

1. **Add Missing Disposal**: `.disposed(by: disposeBag)`
2. **Add Weak Self**: `[weak self]` in closure
3. **Move DisposeBag**: To property level

## Testing the Fix

Suggest verification:
1. Run Instruments with Leaks template
2. Navigate to/from screens multiple times
3. Check Debug Memory Graph for cycles
4. Verify view controllers deallocate

## Reference

**Detailed Examples**: See `examples.md` for extensive code samples and scenarios.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/daispacy) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
