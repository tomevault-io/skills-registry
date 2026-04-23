---
name: ios-sdk-code-review
description: Comprehensive iOS SDK code review for Payoo iOS Frameworks. Checks Clean Architecture patterns, MVVM implementation, UseCase patterns, memory management, naming conventions, API design, and Swift best practices. Use when "review code", "check code", "code review", "review PR", or analyzing Swift files in this project. Use when this capability is needed.
metadata:
  author: daispacy
---

# iOS SDK Code Review

Perform comprehensive code reviews for Payoo iOS Frameworks following Clean Architecture, MVVM, and iOS SDK best practices.

## When to Activate

- "review code", "code review", "check this code"
- "review PR", "review pull request", "review MR"
- "check iOS code", "review Swift code"
- User asks about code quality or best practices
- Reviewing ViewModels, ViewControllers, UseCases, or DataSources

## Review Process

### 1. Identify Code Context

Determine what's being reviewed:
- **File type**: ViewModel, ViewController, UseCase, DataSource, Model, Service
- **Framework**: PayooCore, PayooEwallet, PayooPayment, etc.
- **Layer**: Presentation (Scenes), Domain (UseCase), Data (DataSources/Services)

### 2. Architecture Review

**Clean Architecture Compliance:**
- ✓ Proper layer separation (Presentation/Domain/Data)
- ✓ Dependencies point inward (Presentation → Domain → Data)
- ✓ ViewModels don't directly access Services (must use UseCases)
- ✓ Models in correct layer (Domain models vs Data models)

**MVVM Pattern:**
- ✓ ViewModel has protocol definition (`{Feature}ViewModelType`)
- ✓ Delegate protocol exists (`{Feature}ViewModelDelegate`)
- ✓ ViewController implements delegate
- ✓ ViewModel is testable (no UIKit dependencies)
- ✓ View state managed through delegate callbacks

**UseCase Pattern:**
- ✓ Business logic in UseCases, not ViewModels
- ✓ Single responsibility per UseCase
- ✓ UseCases injected into ViewModels
- ✓ UseCases coordinate repositories/services

### 3. Memory Management Review

**Retain Cycles:**
- ✓ Delegates marked `weak`
- ✓ Closures use `[weak self]` or `[unowned self]` appropriately
- ✓ No strong reference cycles in ViewModels
- ✓ Timer/observer cleanup in `deinit`

**Example issues:**
```swift
// ❌ BAD: Strong delegate reference
var delegate: SomeDelegate?

// ✅ GOOD: Weak delegate reference
weak var delegate: SomeDelegate?

// ❌ BAD: Strong self in closure
viewModel.loadData { data in
    self.updateUI(data)
}

// ✅ GOOD: Weak self in closure
viewModel.loadData { [weak self] data in
    self?.updateUI(data)
}
```

### 4. Dependency Injection Review

**Constructor Injection:**
- ✓ Dependencies injected via `init`
- ✓ All required dependencies in initializer
- ✓ No service locator or singletons (except context)
- ✓ Dependencies are protocols, not concrete types

**Example:**
```swift
// ✅ GOOD: Constructor injection
final class DepositViewModel {
    private let depositAmountUC: DepositAmountUseCase
    private let bankAccountUC: BankAccountUseCase

    init(depositAmountUC: DepositAmountUseCase,
         bankAccountUC: BankAccountUseCase) {
        self.depositAmountUC = depositAmountUC
        self.bankAccountUC = bankAccountUC
    }
}

// ❌ BAD: Service locator pattern
let service = ServiceLocator.shared.depositService
```

### 5. Naming Conventions Review

**File Names:**
- ✓ ViewModels: `{Feature}ViewModel.swift`
- ✓ ViewControllers: `{Feature}ViewController.swift`
- ✓ UseCases: `{Feature}UseCase.swift`
- ✓ DataSources: `{Feature}DataSource.swift`
- ✓ Cells: `{Name}Cell.swift`

**Class/Protocol Names:**
- ✓ Protocols end with `Type` for interfaces: `DepositViewModelType`
- ✓ Delegate protocols end with `Delegate`: `DepositViewModelDelegate`
- ✓ Clear, descriptive names (no abbreviations)
- ✓ Consistent with project conventions

**Variables:**
- ✓ `context` for PayooEwalletContext
- ✓ `{name}UC` for UseCase instances: `depositAmountUC`
- ✓ Descriptive names, avoid single letters (except in loops)

### 6. API Design Review

**For SDK Public APIs:**
- ✓ Clear, self-documenting method names
- ✓ Delegate patterns for callbacks
- ✓ Error handling with proper Error types
- ✓ Thread-safe if needed
- ✓ No force unwrapping in public APIs
- ✓ Proper access control (`public`, `internal`, `private`)

**Error Handling:**
```swift
// ✅ GOOD: Proper error handling
enum DepositError: Error, LocalizedError {
    case outOfRange(bank: String, min: Double, max: Double)

    var errorDescription: String? {
        switch self {
        case .outOfRange(let bank, let min, let max):
            return "Amount out of range for \(bank): \(min)-\(max)"
        }
    }
}

// ❌ BAD: Generic errors
throw NSError(domain: "Error", code: -1, userInfo: nil)
```

### 7. Swift Best Practices

**Code Quality:**
- ✓ No force unwrapping (`!`) unless absolutely safe
- ✓ Use `guard let` for early returns
- ✓ Prefer `let` over `var`
- ✓ Access control appropriately set
- ✓ No commented-out code
- ✓ Proper use of `final` for classes not meant to be subclassed

**SwiftLint Compliance:**
- ✓ Line length ≤ 120 characters
- ✓ File length ≤ 500 lines (warning), ≤ 1200 (error)
- ✓ Type body length ≤ 300 lines (warning), ≤ 400 (error)
- ✓ No trailing whitespace

### 8. Multi-Target Configuration

**Internal/External Builds:**
- ✓ Internal-only code wrapped in `#if INTERNAL`
- ✓ No internal features leaking to external builds
- ✓ Proper preprocessor flag usage

```swift
#if INTERNAL
    // Internal-only features
    func debugFunction() { }
#endif
```

### 9. Localization Review

- ✓ All user-facing strings use `L10n.*` (SwiftGen)
- ✓ No hardcoded strings for UI text
- ✓ Proper format strings for dynamic content

```swift
// ✅ GOOD: Localized strings
let title = L10n.Deposit.Navigation.deposit
let message = L10n.Message.Deposit.outOfRange(min, max, bank)

// ❌ BAD: Hardcoded strings
let title = "Deposit"
```

## Output Format

Provide review as structured report:

```markdown
## Code Review: {FileName}

### ✅ Strengths
- [List what's done well]

### ⚠️ Issues Found

#### 🔴 Critical Issues
**Issue:** [Description]
**Location:** {File}:{Line}
**Impact:** [Why this matters]
**Fix:**
\```swift
// Current code
[problematic code]

// Suggested fix
[fixed code]
\```

#### 🟡 Warnings
**Issue:** [Description]
**Location:** {File}:{Line}
**Suggestion:** [How to improve]

#### 🔵 Suggestions
**Enhancement:** [Description]
**Benefit:** [Why this would help]

### 📊 Summary
- Critical Issues: X
- Warnings: Y
- Suggestions: Z
- Overall: [Pass/Needs Work/Fail]

### 🎯 Priority Actions
1. [Most important fix]
2. [Second priority]
3. [Third priority]
```

## Review Checklists by File Type

### ViewModel Review
- [ ] Has protocol definition (`{Name}ViewModelType`)
- [ ] Has delegate protocol (`{Name}ViewModelDelegate`)
- [ ] Delegate marked `weak`
- [ ] Dependencies injected via `init`
- [ ] No UIKit imports
- [ ] Uses UseCases for business logic
- [ ] Closures use `[weak self]`
- [ ] Testable (no side effects in init)

### ViewController Review
- [ ] Inherits from appropriate base class
- [ ] Implements ViewModel delegate
- [ ] Sets up analytics (`analyticsFeature`, `analyticsScreenName`)
- [ ] Proper lifecycle management
- [ ] IBOutlets are `weak`
- [ ] Navigation setup in `viewDidLoad` or dedicated method
- [ ] No business logic (delegated to ViewModel)

### UseCase Review
- [ ] Single responsibility
- [ ] Injected dependencies
- [ ] No UIKit dependencies
- [ ] Proper error handling with typed errors
- [ ] Testable

### DataSource Review
- [ ] Conforms to UITableView/UICollectionView protocols
- [ ] Clean separation from ViewController
- [ ] Reusable cell registration
- [ ] Proper indexPath handling

## Key Principles

1. **Clean Architecture First**: Verify proper layer separation
2. **Memory Safety**: Check for retain cycles, weak references
3. **Dependency Injection**: Ensure dependencies are injected, not created
4. **Naming Consistency**: Follow project naming conventions
5. **SDK Quality**: Public APIs are well-designed and documented
6. **Testability**: Code is structured for unit testing
7. **Swift Idioms**: Use modern Swift patterns

## Quick Commands

If you need to review specific patterns across the codebase:

```bash
# Find all ViewModels
grep -r "class.*ViewModel" --include="*.swift" PayooEwallet/

# Find potential retain cycles (strong self in closures)
grep -r "{ self\." --include="*.swift" PayooEwallet/

# Find force unwraps
grep -r "!" --include="*.swift" PayooEwallet/ | grep -v "!="

# Find hardcoded strings
grep -r "\"[A-Z]" --include="*.swift" PayooEwallet/ | grep -v "L10n"
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/daispacy) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
