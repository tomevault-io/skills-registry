---
name: ios-app-scaffold
description: | Use when this capability is needed.
metadata:
  author: tddworks
---

# iOS App Scaffold

Scaffold iOS apps with Tuist, Swift 6, and layered architecture.

## Quick Start

Run the scaffold script:
```bash
python3 scripts/scaffold.py <AppName> <output-directory> [--bundle-id <id>] [--team-id <id>]
```

Example:
```bash
python3 scripts/scaffold.py MyApp /Users/me/projects --bundle-id com.mycompany.myapp --team-id ABC123
```

Then generate and open:
```bash
cd /Users/me/projects/MyApp
tuist generate
open MyApp.xcworkspace
```

## Generated Structure

```
AppName/
├── Sources/
│   ├── App/                    # SwiftUI views, app entry
│   │   ├── Views/
│   │   ├── Resources/
│   │   │   ├── Assets.xcassets
│   │   │   ├── XCConfig/       # Build configuration
│   │   │   │   ├── shared.xcconfig
│   │   │   │   ├── debug.xcconfig
│   │   │   │   └── release.xcconfig
│   │   │   └── en.lproj/
│   │   ├── Application/
│   │   ├── Info.plist
│   │   └── AppNameApp.swift
│   ├── Domain/                 # Business logic (no dependencies)
│   │   ├── Models/
│   │   ├── Protocols/          # @Mockable repository interfaces
│   │   └── Utils/
│   └── Infrastructure/         # Persistence implementations
│       └── Local/              # SwiftData repositories
├── Tests/
│   ├── DomainTests/
│   └── InfrastructureTests/
├── Project.swift               # Tuist configuration
├── Tuist.swift
├── .gitignore
└── README.md
```

## Architecture

| Layer | Purpose | Dependencies |
|-------|---------|--------------|
| **Domain** | Models, protocols, business logic | None |
| **Infrastructure** | SwiftData persistence | Domain |
| **App** | SwiftUI views, app entry | Domain, Infrastructure |

For detailed patterns, see [references/architecture.md](references/architecture.md).

## After Scaffolding

1. **Replace example files**: Edit `Example.swift`, `ExampleRepository.swift`, `LocalExampleRepository.swift`
2. **Add domain models**: Create models in `Sources/Domain/Models/`
3. **Define protocols**: Add repository protocols in `Sources/Domain/Protocols/`
4. **Implement persistence**: Add SwiftData entities in `Sources/Infrastructure/Local/`
5. **Build UI**: Create views in `Sources/App/Views/`

## Adding Dependencies

Edit `Project.swift` packages array:
```swift
packages: [
    .remote(url: "https://github.com/Kolos65/Mockable.git", requirement: .upToNextMajor(from: "0.5.0")),
    // Add more packages here
],
```

Then add to target dependencies:
```swift
dependencies: [
    .package(product: "PackageName"),
]
```

## Key Patterns

**Domain models are rich** - Include computed properties and business logic:
```swift
public struct Order: Identifiable, Codable, Sendable {
    public var items: [Item]
    public var total: Decimal { items.reduce(0) { $0 + $1.price } }
}
```

**Protocols use @Mockable** - Enables testing without real persistence:
```swift
@Mockable
public protocol OrderRepository: Sendable {
    func fetchAll() async throws -> [Order]
}
```

**Views consume Domain directly** - No ViewModel layer needed:
```swift
struct OrdersView: View {
    let orders: [Order]  // Domain model directly
}
```

## Version Management

Version numbers are managed in `Project.swift` build settings:

```swift
settings: .settings(
    base: [
        "MARKETING_VERSION": "1.0.0",      // App Store version
        "CURRENT_PROJECT_VERSION": "1",     // Build number
    ],
    ...
)
```

The `Info.plist` references these via build setting variables:
- `CFBundleShortVersionString` → `$(MARKETING_VERSION)`
- `CFBundleVersion` → `$(CURRENT_PROJECT_VERSION)`

To bump version, edit `Project.swift`:
```swift
"MARKETING_VERSION": "1.1.0",
"CURRENT_PROJECT_VERSION": "2",
```

## XCConfig

Build settings are managed via xcconfig files in `Sources/App/Resources/XCConfig/`:

| File | Purpose |
|------|---------|
| `shared.xcconfig` | Common settings (bundle ID, team ID, deployment target) |
| `debug.xcconfig` | Debug configuration (Apple Development signing) |
| `release.xcconfig` | Release configuration (Apple Development signing) |

Edit `shared.xcconfig` to customize:
```
PRODUCT_BUNDLE_IDENTIFIER = com.yourcompany.app
DEVELOPMENT_TEAM = YOUR_TEAM_ID
IPHONEOS_DEPLOYMENT_TARGET = 18.0
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/tddworks) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
