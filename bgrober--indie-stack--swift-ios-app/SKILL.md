---
name: swift-ios-app
description: Use when creating iOS apps, setting up Xcode projects, designing app architecture, implementing SwiftUI views, using SwiftData models, adding Swift 6 concurrency with actors, or integrating Supabase backend. Triggers on "new iOS app", "Swift architecture", "SwiftData setup", "actor pattern", "iOS project structure".
metadata:
  author: bgrober
---

# Swift iOS App Architecture

Guide for building iOS apps with SwiftUI, SwiftData, Swift 6 concurrency, and Supabase backend.

## When to Use

- Starting a new iOS project
- Understanding existing iOS app architecture
- Adding a major new feature that needs architectural guidance
- Reviewing project structure decisions

## Project Structure

```
AppName/
├── AppNameApp.swift          # App entry, configures appearance and SwiftData
├── Models/
│   ├── Item.swift            # SwiftData models (local persistence)
│   ├── ItemResult.swift      # Enum: pending/success/failed states
│   └── Enums/                # Supporting enums (ItemType, etc.)
├── Services/
│   ├── AuthService.swift     # @Observable auth state, trial flow
│   ├── SupabaseClient.swift  # Actor for auth/token management
│   ├── SupabaseService.swift # Actor: storage upload and DB sync
│   ├── SyncService.swift     # Actor: bidirectional sync
│   ├── ItemService.swift     # Actor: business logic for items
│   ├── ImageManager.swift    # Local image caching
│   ├── NetworkMonitor.swift  # NWPathMonitor for connectivity
│   └── PermissionsManager.swift # Camera, photos, etc.
├── Utilities/
│   ├── Constants.swift       # Supabase URL, anon key
│   └── Theme.swift           # Design system, colors, typography
├── ViewModels/
│   └── FeatureViewModel.swift # Complex feature state machines
└── Views/
    ├── RootView.swift        # Routes based on auth state
    ├── MainTabView.swift     # Tab navigation
    ├── SplashView.swift      # Launch animation
    ├── Auth/                 # SignInView, TrialPrompt
    ├── Feature/              # Feature-specific views
    ├── Components/           # Reusable: badges, cards, empty states
    ├── Onboarding/           # OnboardingFlow
    └── Settings/             # SettingsView
```

## Key Patterns

### 1. Swift 6 Concurrency

**Services as Actors:**
```swift
actor SupabaseService {
    private let client: SupabaseClient

    func uploadImage(_ data: Data, path: String) async throws -> URL {
        // Actor-isolated state is thread-safe
    }
}
```

**UI-Bound State with @MainActor:**
```swift
@MainActor @Observable
final class AuthService {
    var isAuthenticated = false
    var currentUser: User?

    func signIn() async throws {
        // All state mutations happen on MainActor
    }
}
```

**Sendable Data Transfer:**
```swift
struct ItemSyncData: Sendable {
    let id: UUID
    let title: String
    let createdAt: Date
}
```

**Bridging SwiftData:**
```swift
@preconcurrency import SwiftData

@Model
final class Item: @unchecked Sendable {
    // All access via MainActor
}
```

### 2. SwiftData Models

**Model with Denormalized Fields:**
```swift
@Model
final class Item {
    var id: UUID
    var createdAt: Date
    var title: String

    // Denormalized for efficient queries
    var resultType: String  // "pending", "success", "failed"
    var score: Int?

    // Full result stored as associated value
    var result: ItemResult = .pending

    init(title: String) {
        self.id = UUID()
        self.createdAt = Date()
        self.title = title
        self.resultType = "pending"
    }
}
```

**Result Enum Pattern:**
```swift
enum ItemResult: Codable, Equatable {
    case pending
    case success(SuccessData)
    case failed(String)
}
```

### 3. Authentication Flow

**AuthService Structure:**
```swift
@MainActor @Observable
final class AuthService {
    private let supabaseClient: SupabaseClient

    var isAuthenticated = false
    var isTrialUsed = false
    var currentUser: User?

    func checkAuthState() async {
        // Check Keychain for existing session
    }

    func signInWithApple() async throws {
        // Apple Sign-In → Supabase Auth
    }

    func signOut() async {
        // Clear Keychain, reset state
    }
}
```

**Root View Routing:**
```swift
struct RootView: View {
    @Environment(AuthService.self) var authService

    var body: some View {
        Group {
            if authService.isAuthenticated {
                MainTabView()
            } else {
                SignInView()
            }
        }
    }
}
```

### 4. Data Sync Pattern

**Upload on Create:**
```swift
// In ViewModel after creating local item
Task {
    await supabaseService.syncItem(item)
}
```

**Download on Login:**
```swift
// In AuthService after successful login
await syncService.syncRemoteItems()
```

**Conflict Resolution:**
```swift
// Use upsert to handle conflicts
func syncItem(_ item: Item) async throws {
    try await client
        .from("items")
        .upsert(item.toRemoteData(), onConflict: "id")
        .execute()
}
```

### 5. Token Refresh Pattern

**Automatic 401 Retry:**
```swift
func makeAuthenticatedRequest<T>(_ request: () async throws -> T) async throws -> T {
    do {
        return try await request()
    } catch let error as HTTPError where error.statusCode == 401 {
        try await refreshToken()
        return try await request()  // Retry once
    }
}
```

## Design System Integration

**Theme.swift Structure:**
```swift
// Colors
extension Color {
    static let appAccent = Color("AccentColor")
    static let cardBackground = Color("CardBackground")
}

// Typography
extension View {
    func appHeadline() -> some View {
        self.font(.system(.headline, design: .rounded))
    }
}

// View Modifiers
extension View {
    func appCard() -> some View {
        self.padding()
            .background(Color.cardBackground)
            .cornerRadius(12)
    }
}

// Haptics
struct HapticManager {
    static func impact(_ style: UIImpactFeedbackGenerator.FeedbackStyle) {
        let generator = UIImpactFeedbackGenerator(style: style)
        generator.impactOccurred()
    }
}
```

## File Naming Conventions

| Type | Convention | Example |
|------|------------|---------|
| SwiftData Model | Singular noun | `Pour.swift` |
| Service Actor | `*Service.swift` | `GradingService.swift` |
| View | `*View.swift` | `CaptureView.swift` |
| ViewModel | `*ViewModel.swift` | `CaptureViewModel.swift` |
| Result Enum | `*Result.swift` | `PourResult.swift` |
| Manager | `*Manager.swift` | `ImageManager.swift` |

## Common Gotchas

### SwiftData + Actors
- Never pass `ModelContext` across actor boundaries
- Use `@preconcurrency import SwiftData`
- Mark models as `@unchecked Sendable` if accessed only via MainActor

### Swift 6 Concurrency
- Use `nonisolated` for pure functions that don't access actor state
- Check `SWIFT_APPROACHABLE_CONCURRENCY = YES` build setting
- Use `Sendable` structs for data transfer between actors

### Supabase Auth
- Store refresh tokens in Keychain, not UserDefaults
- Handle token refresh on 401 automatically
- Use service_role key only in Edge Functions, never in app

## Checklist: New iOS App

```
[ ] Create Xcode project with SwiftData
[ ] Set up folder structure (Models, Services, Views, etc.)
[ ] Add Supabase Swift SDK
[ ] Create Constants.swift with Supabase URL/key
[ ] Create Theme.swift with design system
[ ] Set up AuthService with Apple Sign-In
[ ] Create SupabaseClient actor
[ ] Add NetworkMonitor for connectivity
[ ] Create RootView with auth routing
[ ] Set up MainTabView navigation
[ ] Enable Swift 6 strict concurrency mode
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/bgrober) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
