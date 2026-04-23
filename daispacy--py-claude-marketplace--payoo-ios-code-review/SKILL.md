---
name: payoo-ios-code-review
description: Comprehensive iOS code review for Payoo Merchant app. Checks Clean Architecture patterns, MVVM with RxSwift, memory management, Swinject DI, session error handling, layer separation, naming conventions, and SwiftLint compliance. Use when "review code", "check code", "code review", "review PR", "check pull request", or analyzing Swift files in this project. Use when this capability is needed.
metadata:
  author: daispacy
---

# Payoo iOS Code Review

Comprehensive code review for the Payoo Merchant iOS app following Clean Architecture with RxSwift and Swinject.

## When to Activate

- "review code", "check code", "code review"
- "review PR", "review pull request", "check pull request"
- "review this file", "check this ViewModel"
- "is this code correct", "any issues with this code"
- When analyzing Swift files in PayooMerchant, Domain, Data, or Analytics layers

## Review Process

### Step 1: Identify Scope
- Single file review → Read the file
- Multiple files → Use Glob to find related files
- Pull request → Check git diff for changed files
- Full feature → Grep for related ViewModels/UseCases

### Step 2: Layer-Specific Checks

#### For Presentation Layer (PayooMerchant/)
1. **MVVM Pattern**
   - ✓ ViewModel implements `ViewModelType` protocol
   - ✓ Has `Input` and `Output` nested types
   - ✓ Has `transform(input:) -> Output` method
   - ✓ ViewControllers bind to Input/Output only
   - ✓ No business logic in ViewControllers

2. **RxSwift Memory Management**
   - ✓ Every ViewController/ViewModel has `DisposeBag`
   - ✓ All subscriptions use `.disposed(by: disposeBag)`
   - ✓ Closures capturing self use `[weak self]` or `[unowned self]`
   - ✓ No retain cycles in Observable chains

3. **Navigation & DI**
   - ✓ Navigator passed as dependency (never created directly)
   - ✓ UseCases injected via constructor
   - ✓ No direct ViewController instantiation
   - ✓ Uses factory methods from `ViewControllerFactory`

4. **Session Error Handling**
   - ✓ **CRITICAL**: All API calls have `.catchSessionError(sessionUC)`
   - ✗ Missing `.catchSessionError()` → Session timeout won't logout

#### For Domain Layer (Domain/)
1. **Clean Architecture Rules**
   - ✓ Pure Swift only (no UIKit imports)
   - ✓ No imports from Data or Presentation layers
   - ✓ Only protocols for services (no implementations)
   - ✓ Models are simple structs/classes

2. **UseCase Pattern**
   - ✓ Protocol defines interface (`UseCaseType`)
   - ✓ Implementation injected with dependencies
   - ✓ Single responsibility per UseCase
   - ✓ Returns RxSwift Observables/Singles/Maybes
   - ✓ Uses `.catchSessionError(sessionUC)` for API calls

3. **Service Protocols**
   - ✓ Defined in `Domain/Service/`
   - ✓ Implemented in Data layer
   - ✓ Injected via Swinject

#### For Data Layer (Data/)
1. **Repository Pattern**
   - ✓ Implements Domain service protocols
   - ✓ Uses Moya for network calls
   - ✓ Uses Realm for local storage
   - ✓ Converters transform DTOs ↔ Domain models

2. **API Models**
   - ✓ DTOs in `Data/Model/`
   - ✓ Conform to `DomainConvertible` or `RealmRepresentable`
   - ✓ Use ObjectMapper for JSON parsing
   - ✓ Don't leak to Domain/Presentation layers

### Step 3: Project-Wide Checks

1. **SwiftLint Compliance**
   - Run: `./Pods/SwiftLint/swiftlint lint --reporter xcode`
   - Check: Type body length (300/400), file length (800/1200)
   - Check: Opt-in rules (empty_count, yoda_condition, todo, etc.)

2. **Common Pitfalls**
   - [ ] Missing `.catchSessionError()` on API observables
   - [ ] Manual ViewController instantiation (should use factory)
   - [ ] Missing DependencyContainer registration
   - [ ] Breaking layer boundaries (e.g., Data imported in Domain)
   - [ ] Missing `disposed(by: disposeBag)`
   - [ ] Strong self in closures causing retain cycles
   - [ ] Using `.count > 0` instead of `.isEmpty` (SwiftLint)
   - [ ] Force unwraps without justification
   - [ ] Magic numbers without constants

3. **RxSwift Best Practices**
   - Use `Driver` for UI bindings (never fails, main thread)
   - Use `Single` for one-time operations (network calls)
   - Use `Observable` for streams
   - Use `Maybe` for optional single values
   - Prefer `.bind(to:)` over `.subscribe(onNext:)`

4. **Naming Conventions**
   - [ ] ViewModels: `[Feature]ViewModel` (e.g., `LoginViewModel`, `TransactionHistoryViewModel`)
   - [ ] ViewControllers: `[Feature]ViewController` (e.g., `LoginViewController`)
   - [ ] UseCases: `[Action]UseCase` (e.g., `GetProfileUseCase`, `LoginUseCase`)
   - [ ] UseCase protocols: `[Action]UseCaseType` (e.g., `GetProfileUseCaseType`)
   - [ ] Navigators: `[Feature]Navigator` (e.g., `LoginNavigator`, `HomeNavigator`)
   - [ ] Navigator protocols: `[Feature]NavigatorType`
   - [ ] Services (protocols): `[Name]Service` (e.g., `ApiService`, `LocalStorageService`)
   - [ ] Services (impl): `Default[Name]Service` or `[Tech][Name]Service` (e.g., `DefaultApiService`, `RealmStorageService`)
   - [ ] Protocols: `[Name]Type` suffix for main protocols
   - [ ] Variables: camelCase, descriptive (avoid abbreviations like `usrNm`, use `username`)
   - [ ] Constants: camelCase for local, or `k` prefix for global (e.g., `kMaxRetryCount`)
   - [ ] IBOutlets: Descriptive names with type suffix (e.g., `loginButton`, `usernameTextField`)
   - [ ] Avoid single letters except in loops (i, j) or common conventions (x, y)

### Step 4: Generate Report

Format:
```markdown
## Code Review: [File/Feature Name]

### 📋 Summary
Files: X | 🔴 Critical: X | 🟡 Warning: X | 🔵 Info: X | Status: [✅ Approved / ⚠️ Needs fixes / ❌ Blocked]

### ✅ Strengths
- [List good patterns found]

### ⚠️ Issues Found

#### 🔴 Critical (Must Fix)
**[Issue]** at [file:line]
- **Problem**: [Description]
- **Impact**: [Why critical]
- **Fix**:
\`\`\`swift
// Corrected code
\`\`\`

#### 🟡 Warning (Should Fix)
**[Issue]** at [file:line]
- **Problem**: [Description]
- **Suggestion**: [How to fix]

#### 🔵 Info (Consider)
**[Issue]** at [file:line]
- **Note**: [Observation]
- **Suggestion**: [Optional improvement]
```

## Review Categories

### Critical Issues (Must Fix)
- Missing `.catchSessionError()` on API calls
- Retain cycles / memory leaks
- Breaking Clean Architecture layer boundaries
- Missing DisposeBag disposal
- Force unwraps in unsafe contexts

### Warnings (Should Fix)
- Manual ViewController instantiation
- Missing DependencyContainer registration
- SwiftLint violations
- Non-descriptive variable names
- Large type bodies (>300 lines)

### Info (Consider)
- Potential optimizations
- Code duplication
- Missing unit tests
- Outdated comments
- TODO/FIXME comments

## Quick Commands

Run SwiftLint:
```bash
./Pods/SwiftLint/swiftlint lint --reporter xcode
```

Find files without DisposeBag:
```bash
grep -L "DisposeBag" PayooMerchant/**/*ViewModel.swift
```

Find API calls without catchSessionError:
```bash
grep -r "apiService\." --include="*.swift" | grep -v "catchSessionError"
```

## Example Review Flow

1. User: "Review LoginViewModel"
2. Read `PayooMerchant/Controllers/Login/LoginViewModel.swift`
3. Check MVVM pattern, RxSwift, DI
4. Grep for related files (LoginViewController, LoginUseCase)
5. Run SwiftLint on the file
6. Generate detailed report with line numbers
7. Provide fix recommendations

## Key Architectural Rules

1. **Layer Dependencies**
   ```
   Presentation → Domain ← Data
   ```
   - Presentation can import Domain
   - Data can import Domain
   - Domain imports nothing (pure Swift)
   - NEVER: Domain imports Data/Presentation

2. **RxSwift Pattern**
   ```swift
   // ViewModel transform pattern
   func transform(input: Input) -> Output {
       let result = input.trigger
           .flatMapLatest { [weak self] _ -> Observable<Data> in
               guard let self = self else { return .empty() }
               return self.useCase.execute()
                   .catchSessionError(self.sessionUC) // CRITICAL!
           }
       return Output(result: result.asDriver(onErrorJustReturn: .empty))
   }
   ```

3. **Memory Management**
   ```swift
   // CORRECT
   .subscribe(onNext: { [weak self] value in
       self?.updateUI(value)
   }).disposed(by: disposeBag)

   // WRONG - Retain cycle!
   .subscribe(onNext: { value in
       self.updateUI(value)
   }).disposed(by: disposeBag)
   ```

## Output Format

Always provide:
1. Clear issue categorization (Critical/Warning/Info)
2. File paths with line numbers for clickable links
3. Code snippets showing the problem
4. Concrete fix recommendations
5. Summary with metrics

Reference: See `standards.md` for detailed coding standards and `examples.md` for review examples.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/daispacy) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
