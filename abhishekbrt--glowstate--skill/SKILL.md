---
name: building-flutter-apps
description: Build production-ready Flutter apps for Android/iOS using feature-first architecture. Covers project setup, UI patterns, state management (Riverpod/BLoC), navigation (go_router), testing (TDD with mocktail), and deployment. Use when creating Flutter projects, implementing features, debugging Flutter issues, or making architectural decisions. Use when this capability is needed.
metadata:
  author: abhishekbrt
---

# Building Flutter Apps

## Quick Start

```bash
flutter create --org com.yourcompany --project-name my_app ./my_app
cd my_app && flutter run
```

## Skill Guides

| Area | Guide | Use When |
|------|-------|----------|
| Architecture | [architecture/SKILL.md](architecture/SKILL.md) | Project structure, DI, repository pattern |
| UI Building | [ui/SKILL.md](ui/SKILL.md) | Layouts, Material 3, responsive design |
| State Management | [state-management/SKILL.md](state-management/SKILL.md) | Riverpod, BLoC, state patterns |
| Testing | [testing/SKILL.md](testing/SKILL.md) | TDD, unit/widget tests, mocking |
| Project Setup | [project-setup.md](project-setup.md) | New projects, pubspec, flavors |
| Navigation | [navigation.md](navigation.md) | go_router, deep links, transitions |
| Animations | [animations.md](animations.md) | Implicit, explicit, Hero animations |
| Performance | [performance.md](performance.md) | Optimization, profiling, app size |
| Deployment | [deployment.md](deployment.md) | App store builds, CI/CD, signing |
| Platform Integration | [platform-integration.md](platform-integration.md) | Platform channels, permissions |
| Packages | [packages.md](packages.md) | Essential packages, creating plugins |

## Feature-First Project Structure

```
lib/
├── main.dart
├── app.dart                    # MaterialApp configuration
├── core/                       # Shared across all features
│   ├── providers/              # Core Riverpod providers
│   │   ├── api_client_provider.dart
│   │   └── shared_preferences_provider.dart
│   ├── network/api_client.dart
│   ├── error/failures.dart
│   ├── theme/app_theme.dart
│   └── widgets/                # Truly reusable widgets only
├── features/
│   ├── auth/
│   │   ├── data/
│   │   │   ├── datasources/
│   │   │   ├── models/
│   │   │   ├── repositories/auth_repository_impl.dart
│   │   │   └── providers/auth_repository_provider.dart
│   │   ├── domain/
│   │   │   ├── entities/
│   │   │   └── repositories/auth_repository.dart  # Interface
│   │   └── presentation/
│   │       ├── providers/auth_provider.dart
│   │       ├── screens/
│   │       └── widgets/
│   ├── home/
│   │   ├── data/
│   │   ├── domain/
│   │   └── presentation/
│   └── [other_features]/
└── config/
    ├── routes.dart
    └── environment.dart
```

## Decision Guides

### What to Build?

| Task | Start Here |
|------|------------|
| New project | [project-setup.md](project-setup.md) → [architecture/](architecture/SKILL.md) |
| New feature | [architecture/](architecture/SKILL.md) → Write interface → TDD |
| UI screen | [ui/](ui/SKILL.md) → [state-management/](state-management/SKILL.md) |
| Fix performance | [performance.md](performance.md) |
| Release app | [deployment.md](deployment.md) |

### State Management Choice

| Scenario | Use |
|----------|-----|
| Form input, toggle, local UI state | `setState` |
| Single value shared across widgets | `ValueNotifier` or `StateProvider` |
| Feature with loading/error states | `AsyncNotifierProvider` (Riverpod) |
| Mutable state with business logic | `NotifierProvider` (Riverpod) |
| Complex event flows, event tracking | `BLoC` (alternative) |

## Essential Commands

```bash
flutter pub get                 # Install dependencies
flutter run                     # Debug mode
flutter test                    # Run tests
flutter build apk --release     # Android release
flutter build ipa --release     # iOS release
flutter clean && flutter pub get  # Reset project

# Riverpod code generation
dart run build_runner build --delete-conflicting-outputs
dart run build_runner watch --delete-conflicting-outputs
```

## TDD Workflow (from AGENTS.md)

```
1. Interface First   → Define contract in domain/repositories/
2. RED Phase         → Write failing test, implementation throws UnimplementedError
3. GREEN Phase       → Write minimum code to pass
4. REFACTOR          → Clean up, add edge cases
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/abhishekbrt) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
