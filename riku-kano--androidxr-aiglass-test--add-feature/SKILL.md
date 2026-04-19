---
name: add-feature
description: Add a new feature following official Android architecture (UI/Domain/Data Layer, MVVM, UDF). Use when this capability is needed.
metadata:
  author: riku-kano
---

# Add Feature (Android Architecture Compliant)

Design and implement a new feature following the official Android recommended architecture.

## Step 1: Requirement Analysis
- Clarify the user's request
- Ask questions if anything is unclear
- Investigate the impact on existing code

## Step 2: Architecture Design

Place files according to this layer structure:

```
feature-name/
├── data/
│   ├── repository/          # Repository implementation
│   │   └── XxxRepositoryImpl.kt
│   ├── datasource/          # DataSource (API, DB, etc.)
│   │   ├── remote/
│   │   └── local/
│   └── model/               # Data layer models (DTO, Entity)
├── domain/                  # (if needed) Use Cases
│   ├── usecase/
│   │   └── XxxUseCase.kt
│   ├── model/               # Domain models
│   └── repository/          # Repository interfaces
│       └── XxxRepository.kt
└── ui/                      # UI Layer
    ├── XxxScreen.kt         # Composable (stateless)
    ├── XxxViewModel.kt      # ViewModel (StateFlow<UiState>)
    └── XxxUiState.kt        # UI State (data class / sealed interface)
```

### Design Principles
- **UDF (Unidirectional Data Flow)**: State flows down, events flow up
- **ViewModel**: Exposes a single `StateFlow<UiState>`. Use `stateIn(SharingStarted.WhileSubscribed(5_000))`
- **UI State**: Defined as a `data class` holding the entire screen state
- **User Actions**: Defined as `sealed interface`, processed by ViewModel's `onAction(action)` method
- **Repository**: Single source of truth for data. Separate interface from implementation
- **DI**: Hilt (`@HiltViewModel`, `@Inject constructor`). Prefer constructor injection

## Step 3: Implementation Rules

### Compose UI
- Keep Composables stateless (state hoisting)
- Use ViewModel only in screen-level Composables
- Collect StateFlow with `collectAsStateWithLifecycle()`
- Define strings in `strings.xml`
- Always add `@Preview` functions

### Kotlin Style
- Define state/models as `data class`
- Use `sealed class`/`sealed interface` for finite types
- Naming: Class=`PascalCase`, Function=`camelCase`, Constants=`SCREAMING_SNAKE_CASE`

### Dependency Management
1. Add version + library to `gradle/libs.versions.toml`
2. Reference as `implementation(libs.xxx)` in `app/build.gradle.kts`
3. Never hardcode versions directly

### Navigation
- Define routes as `@Serializable` data class/object (type-safe navigation)
- Manage `NavController` at the top level; pass callbacks to child Composables

### Permissions (if needed)
1. Add `<uses-permission>` to `AndroidManifest.xml`
2. Request runtime permissions with the appropriate Contract

## Step 4: Testing
- ViewModel unit tests (`runTest`, `TestDispatcher`)
- Repository tests (using Fake DataSource)
- `@Preview` for Composables
- UI tests if needed

## Step 5: Build Verification
```bash
./gradlew assembleDebug
./gradlew test
./gradlew lint
```

## Feature to Add

$ARGUMENTS

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/riku-kano) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
