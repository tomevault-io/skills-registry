---
name: swift-testing
description: > Use when this capability is needed.
metadata:
  author: dboone323
---

# Swift Testing Expert (Project-Specific)

Testing expertise tailored for the iOS/macOS apps in this monorepo.

## Project Test Structure

```
ProjectName/
├── ProjectName/           # Source files
├── ProjectNameTests/      # Unit tests
└── ProjectNameUITests/    # UI tests (if applicable)
```

## Test Conventions

### File Naming

- Test file: `[SourceFile]Tests.swift`
- Example: `GameScene.swift` → `GameSceneTests.swift`

### Test Function Naming

```swift
func test_[unit]_[scenario]_[expectedResult]()
// Example:
func test_scoreManager_addPoints_incrementsScore()
```

### Standard Patterns

```swift
import XCTest
@testable import ProjectName

final class ComponentTests: XCTestCase {
    var sut: Component!  // System Under Test

    override func setUp() {
        super.setUp()
        sut = Component()
    }

    override func tearDown() {
        sut = nil
        super.tearDown()
    }

    func test_method_condition_expectedResult() {
        // Arrange

        // Act

        // Assert
    }
}
```

### Async Testing

```swift
func test_asyncMethod_succeeds() async throws {
    let result = try await sut.fetchData()
    XCTAssertNotNil(result)
}
```

### Memory Safety

```swift
// Use [weak self] in closures
Task { [weak self] in
    guard let self else { return }
    await self.performWork()
}
```

## Running Tests

```bash
# Via Xcode
Cmd+U  # Run all tests

# Via command line
xcodebuild test -scheme ProjectName -destination 'platform=iOS Simulator,name=iPhone 16'
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/dboone323) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
