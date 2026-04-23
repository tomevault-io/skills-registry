---
name: ios-code-review
description: Concise iOS code review for Payoo Merchant app. Focuses on critical/high/medium issues - RxSwift memory leaks, retain cycles, naming conventions, Clean Architecture violations, and business logic placement. Use when reviewing Swift files, pull requests, ViewModels, ViewControllers, UseCases, and Repositories. Use when this capability is needed.
metadata:
  author: daispacy
---

# iOS Code Review - Priority Issues Focus

Expert iOS code reviewer for Payoo Merchant application, focusing on CRITICAL, HIGH, and MEDIUM priority issues that impact app stability, maintainability, and architecture.

## When to Activate

- "review code", "check this file", "review PR"
- Mentions Swift files: ViewController, ViewModel, UseCase, Repository
- "code quality", "best practices", "check standards"
- RxSwift memory leaks, retain cycles, Clean Architecture, MVVM patterns

## Review Process

### Step 1: Identify Scope
Determine what to review:
- Specific files (e.g., "PaymentViewModel.swift")
- Directories (e.g., "Payment module")
- Git changes (recent commits, PR diff)
- Entire module or feature

### Step 2: Read and Analyze
Use Read tool to examine files, focusing on CRITICAL, HIGH, and MEDIUM priority issues only.

### Step 3: Apply Priority Standards

## 🎯 PRIORITY FOCUS AREAS

### 1. RxSwift Memory Leaks 🔴 CRITICAL
**Impact**: Memory leaks, app crashes, performance degradation

**Check for**:
- **Missing disposal**: Every `.subscribe()` MUST have `.disposed(by: disposeBag)`
- **Retain cycles**: Use `[weak self]` in all closures capturing `self`
- **DisposeBag**: Must be declared as instance variable, not local
- **Observable chains**: No abandoned subscriptions

**Common violations**:
```swift
// ❌ CRITICAL - Memory leak
observable
    .subscribe(onNext: { value in
        self.updateUI(value) // Missing disposed(by:)
    })

// ❌ CRITICAL - Retain cycle
observable
    .subscribe(onNext: { [self] value in // Strong self!
        self.updateUI(value)
    })
    .disposed(by: disposeBag)

// ✅ GOOD
observable
    .subscribe(onNext: { [weak self] value in
        self?.updateUI(value)
    })
    .disposed(by: disposeBag)
```

### 2. Naming Conventions 🟠 HIGH
**Impact**: Code readability, maintainability, team collaboration

**Check for**:
- **Types**: PascalCase, descriptive (e.g., `PaymentViewModel`, not `PmtVM`)
- **Variables**: camelCase (e.g., `paymentAmount`, not `pmt_amt`)
- **Booleans**: Must have `is`/`has`/`should`/`can` prefix (e.g., `isLoading`, not `loading`)
- **NO abbreviations** except URL, ID, VC (ViewController), UC (UseCase)
- **IBOutlets**: Must include type suffix (e.g., `amountTextField`, not `amount`)

**Common violations**:
```swift
// ❌ BAD
var usr: User?
var loading = false
@IBOutlet weak var amount: UITextField!
var pmtVM: PaymentViewModel?

// ✅ GOOD
var user: User?
var isLoading = false
@IBOutlet weak var amountTextField: UITextField!
var paymentViewModel: PaymentViewModel?
```

### 3. Clean Architecture Violations 🟠 HIGH
**Impact**: Testability, maintainability, architecture integrity

**Check for**:
- **ViewModels**: Must extend `BaseViewModel<State>`, NO business logic
- **ViewModel → UseCase**: ViewModels MUST call UseCases, NEVER call Repository/API directly
- **Business logic**: Must be in UseCases ONLY, not in ViewModel/ViewController
- **Dependency injection**: All dependencies via constructor (Swinject)
- **Layer separation**: ViewModel → UseCase → Repository → DataSource

**Common violations**:
```swift
// ❌ BAD - ViewModel calling Repository directly
class PaymentViewModel {
    private let repository: PaymentRepository

    func loadPayments() {
        repository.getPayments() // ❌ Skip UseCase layer
            .subscribe(onNext: { payments in
                // ...
            })
            .disposed(by: disposeBag)
    }
}

// ❌ BAD - Business logic in ViewModel
class PaymentViewModel {
    func processPayment(amount: Double) {
        if amount <= 0 { return } // ❌ Business logic!
        let fee = amount * 0.02
        let total = amount + fee
        // ...
    }
}

// ✅ GOOD - Proper Clean Architecture
class PaymentViewModel: BaseViewModel<PaymentState> {
    private let getPaymentsUseCase: GetPaymentsUseCase
    private let processPaymentUseCase: ProcessPaymentUseCase

    init(getPaymentsUseCase: GetPaymentsUseCase,
         processPaymentUseCase: ProcessPaymentUseCase) {
        self.getPaymentsUseCase = getPaymentsUseCase
        self.processPaymentUseCase = processPaymentUseCase
        super.init()
    }

    func loadPayments() {
        getPaymentsUseCase.execute() // ✅ Call UseCase
            .subscribe(onNext: { [weak self] payments in
                self?.state.accept(.success(payments))
            })
            .disposed(by: disposeBag)
    }
}
```

### 4. RxSwift Scheduler Issues 🟡 MEDIUM
**Impact**: UI freezes, background thread crashes

**Check for**:
- **Background work**: Use `.subscribeOn(ConcurrentDispatchQueueScheduler(qos: .background))`
- **UI updates**: Must use `.observeOn(MainScheduler.instance)` before UI work
- **Never block main thread**: Network/DB operations on background scheduler

**Common violations**:
```swift
// ❌ BAD - Heavy work on main thread
apiService.getData()
    .subscribe(onNext: { data in
        // Heavy processing on main thread
        self.processLargeData(data)
    })
    .disposed(by: disposeBag)

// ✅ GOOD - Proper scheduler usage
apiService.getData()
    .subscribeOn(ConcurrentDispatchQueueScheduler(qos: .background))
    .map { data in
        // Heavy processing on background
        return self.processLargeData(data)
    }
    .observeOn(MainScheduler.instance)
    .subscribe(onNext: { [weak self] result in
        // UI updates on main
        self?.updateUI(result)
    })
    .disposed(by: disposeBag)
```

### 5. Error Handling 🟡 MEDIUM
**Impact**: App crashes, poor UX

**Check for**:
- **Observable chains**: Must handle `.onError` or use `catchError`
- **API calls**: All network operations must have error handling
- **User feedback**: Show error messages to user

**Common violations**:
```swift
// ❌ BAD - No error handling
apiService.getData()
    .subscribe(onNext: { data in
        self.updateUI(data)
    })
    .disposed(by: disposeBag)

// ✅ GOOD - Proper error handling
apiService.getData()
    .subscribe(
        onNext: { [weak self] data in
            self?.updateUI(data)
        },
        onError: { [weak self] error in
            self?.showError(error.localizedDescription)
        }
    )
    .disposed(by: disposeBag)

// ✅ BETTER - Using catchError
apiService.getData()
    .catchError { error in
        return Observable.just(defaultData)
    }
    .subscribe(onNext: { [weak self] data in
        self?.updateUI(data)
    })
    .disposed(by: disposeBag)
```

### 6. Deprecated Patterns 🟡 MEDIUM
**Impact**: Future compatibility, best practices

**Check for**:
- **Use BehaviorRelay/PublishRelay** instead of BehaviorSubject/PublishSubject
- **Avoid Variable**: Use BehaviorRelay instead

**Common violations**:
```swift
// ❌ BAD - Using deprecated BehaviorSubject
private let loadingSubject = BehaviorSubject<Bool>(value: false)

// ✅ GOOD - Use BehaviorRelay
private let loadingRelay = BehaviorRelay<Bool>(value: false)
```

### Step 4: Generate Concise Report

Focus ONLY on CRITICAL (🔴), HIGH (🟠), and MEDIUM (🟡) priority issues. Skip low priority findings.

Provide concise, actionable output with:
- **Summary**: Only 🔴/🟠/🟡 counts (one line per severity)
- **Issues**: Group by severity, concise title + file + line number
- **Code snippets**: Only for Critical/High issues, keep minimal
- **Quick fixes**: Brief, actionable recommendations

## Severity Levels - CRITICAL/HIGH/MEDIUM ONLY

🔴 **CRITICAL** - Fix immediately (blocks release)
- **Missing disposal**: `.subscribe()` without `.disposed(by: disposeBag)` → Memory leak
- **Retain cycles**: Strong `self` in closures → Memory leak
- **UI on background thread**: UI updates not on MainScheduler → Crash risk

🟠 **HIGH PRIORITY** - Fix before merge
- **Naming violations**: Abbreviations, wrong case, missing is/has prefix, IBOutlet without type suffix
- **Architecture violations**: ViewModel calling Repository/API directly (skipping UseCase)
- **Business logic misplacement**: Business logic in ViewModel/ViewController instead of UseCase
- **Missing BaseViewModel**: ViewModel not extending `BaseViewModel<State>`

🟡 **MEDIUM PRIORITY** - Fix in current sprint
- **No error handling**: Observable chains without onError or catchError
- **Wrong schedulers**: Heavy work on main thread, missing observeOn(MainScheduler)
- **Deprecated patterns**: Using BehaviorSubject/PublishSubject instead of Relay

## 🚫 IGNORE (Out of Scope)
- Code style and formatting (handled by SwiftLint)
- Documentation and comments
- Accessibility (unless critical)
- Security issues (separate review)
- Performance optimizations (unless critical)
- UI/UX improvements
- Low priority issues

## Output Format

**KEEP IT CONCISE** - Focus on actionable findings only.

```markdown
# iOS Code Review - Priority Issues

## Summary
🔴 Critical: X | 🟠 High: X | 🟡 Medium: X

## 🔴 CRITICAL ISSUES (Fix Immediately)

1. **Memory leak - Missing disposal** - `PaymentViewModel.swift:45`
   ```swift
   // ❌ observable.subscribe(onNext: { ... }) // No disposal
   // ✅ .disposed(by: disposeBag)
   ```

2. **Retain cycle - Strong self** - `TransactionViewController.swift:78`
   - Use `[weak self]` instead of `[self]`

---

## 🟠 HIGH PRIORITY (Fix Before Merge)

1. **Naming - Abbreviations** - `UserViewModel.swift:12`
   - `usr` → `user`, `pmtVM` → `paymentViewModel`

2. **Architecture - ViewModel calls Repository directly** - `PaymentViewModel.swift:34`
   - Inject and call `GetPaymentsUseCase` instead of `PaymentRepository`

3. **Business logic in ViewModel** - `PaymentViewModel.swift:56-60`
   - Move fee calculation to `ProcessPaymentUseCase`

---

## 🟡 MEDIUM PRIORITY (Fix This Sprint)

1. **No error handling** - `DashboardViewModel.swift:89`
   - Add `onError` or `catchError` to API call

2. **Wrong scheduler** - `TransactionListViewModel.swift:123`
   - Add `.observeOn(MainScheduler.instance)` before UI update

3. **Deprecated BehaviorSubject** - `SettingsViewModel.swift:23`
   - Use `BehaviorRelay` instead

---

## ⚠️ Action Required
- 🔴 **X Critical** - Block release, fix now
- 🟠 **X High** - Block merge, fix today
- 🟡 **X Medium** - Fix in current sprint

✅ **Well Done**: [If applicable, briefly acknowledge 1-2 good patterns]
```

## Quick Reference

**Focus on 6 Priority Areas**:
1. 🔴 **RxSwift Memory Leaks** - Missing disposal, retain cycles
2. 🟠 **Naming Conventions** - Abbreviations, wrong case, missing prefixes
3. 🟠 **Clean Architecture** - ViewModel → UseCase → Repository flow
4. 🟡 **Schedulers** - Background work, main thread UI updates
5. 🟡 **Error Handling** - onError, catchError in Observable chains
6. 🟡 **Deprecated Patterns** - BehaviorRelay vs BehaviorSubject

**Skip**: Code style, docs, accessibility, security, performance (unless critical), UI/UX, low priority

## Tips

- **Be concise**: One-line issue descriptions when possible
- **Be specific**: Exact file paths and line numbers
- **Be actionable**: Clear fix instructions
- **Show code**: Only for Critical/High issues, keep minimal
- **Group issues**: Batch similar violations (e.g., "5 naming violations in PaymentViewModel.swift:12,34,56,78,90")
- **No explanations**: Skip "Why" unless unclear - developers know why memory leaks are bad

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/daispacy) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
