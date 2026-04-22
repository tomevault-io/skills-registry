---
name: flutter-clean-arch
description: Flutter Clean Architecture skill for building scalable mobile apps. Use when creating new Flutter projects, implementing BLoC pattern, setting up Dio networking, or following clean architecture patterns with model_view/views structure. Use when this capability is needed.
metadata:
  author: funnyhust
---

# Flutter Clean Architecture

A comprehensive skill for building Flutter applications following Clean Architecture principles, based on real-world production patterns.

## When to Use This Skill

- Creating a new Flutter project with clean architecture
- Setting up Dio networking with interceptors (auth, logging)
- Implementing BLoC/Cubit state management pattern
- Configuring dependency injection with GetIt
- Setting up GoRouter navigation
- Managing environment variables with flutter_dotenv
- Implementing secure storage for tokens
- Setting up Android/iOS permissions and security
- Working with code generation (freezed, json_serializable)

## Decision Tree - What Are You Implementing?

Use this decision tree to find the right guide for your task:

```
What are you implementing?
│
├── 🆕 New Flutter Project
│   ├── Project structure → See [Project Structure](examples/project-structure.md)
│   ├── Dependencies → See [pubspec Template](resources/pubspec-template.yaml)
│   ├── Environment setup → See [Environment Setup](resources/environment-setup.md)
│   └── Native splash → See [Native Splash Setup](examples/native-splash-setup.md)
│
├── 📡 API / Networking
│   ├── Dio client setup → See [Dio Setup](examples/dio-setup.md)
│   ├── Remote data source → See [Remote DataSource Pattern](examples/remote-datasource-pattern.md)
│   └── Repository layer → See [Repository Pattern](examples/repository-pattern.md)
│
├── 📦 Models / Data
│   ├── Model with freezed → See [Freezed & JSON Serializable](examples/freezed-json-serializable.md)
│   ├── Request/Response models → See [Repository Pattern](examples/repository-pattern.md)
│   └── Build runner commands → See [Build Runner Guide](examples/build-runner-guide.md)
│
├── 🔄 State Management
│   ├── Cubit pattern → See [BLoC/Cubit Example](examples/bloc-cubit-example.md)
│   ├── BLoC pattern → See [BLoC/Cubit Example](examples/bloc-cubit-example.md)
│   └── State with freezed → See [Freezed & JSON Serializable](examples/freezed-json-serializable.md)
│
├── 💉 Dependency Injection
│   └── GetIt setup → See [Dependency Injection](examples/dependency-injection.md)
│
├── 🧭 Navigation
│   └── GoRouter setup → See [Navigation Setup](examples/navigation-setup.md)
│
├── 📱 Platform Config
│   ├── Android permissions → See [Android Permissions](resources/android-permissions.md)
│   ├── iOS permissions → See [iOS Permissions](resources/ios-permissions.md)
│   └── Security checklist → See [Security Checklist](resources/security-checklist.md)
│
└── 🛠️ Code Generation
    ├── Freezed models → See [Freezed & JSON Serializable](examples/freezed-json-serializable.md)
    ├── JSON serializable → See [Freezed & JSON Serializable](examples/freezed-json-serializable.md)
    └── Build runner → See [Build Runner Guide](examples/build-runner-guide.md)
```

## Project Structure Overview

```
lib/
├── main.dart                          # Entry point
├── app_root.dart                      # App root with MaterialApp
├── app_providers.dart                 # Global BLoC providers
├── core/                              # Core utilities (environment, theme, widgets)
├── utils/                             # Helpers, extensions, validators
├── data/
│   ├── data_sources/
│   │   ├── api/                       # Dio, interceptors, api_path
│   │   ├── local/                     # SharedPreferences, SecureStorage
│   │   └── remote/                    # Feature data sources
│   ├── models/                        # Data models
│   └── repositories/                  # Repository implementations
├── model_view/                        # BLoC/Cubit state management
├── views/                             # UI pages
├── di/                                # Dependency injection
└── route/                             # Navigation
```

> For full structure details → See [Project Structure](examples/project-structure.md)

## Quick Reference

### Adding a New Feature

1. **Create model** in `data/models/[feature]/`
   → See [Freezed & JSON Serializable](examples/freezed-json-serializable.md)

2. **Create remote data source** in `data/data_sources/remote/`
   → See [Remote DataSource Pattern](examples/remote-datasource-pattern.md)

3. **Create repository** in `data/repositories/[feature]/`
   → See [Repository Pattern](examples/repository-pattern.md)

4. **Create cubit** in `model_view/[feature]/`
   → See [BLoC/Cubit Example](examples/bloc-cubit-example.md)

5. **Create page** in `views/[feature]/`

6. **Register in DI** in `di/[feature]_inject.dart`
   → See [Dependency Injection](examples/dependency-injection.md)

7. **Add route** in `route/app_route.dart`
   → See [Navigation Setup](examples/navigation-setup.md)

### Common Commands

```bash
# Code generation
flutter pub run build_runner build --delete-conflicting-outputs

# Watch mode (development)
flutter pub run build_runner watch --delete-conflicting-outputs

# Generate native splash
flutter pub run flutter_native_splash:create
```

> For more commands → See [Build Runner Guide](examples/build-runner-guide.md)

## Examples & Resources

### Examples
- [Project Structure](examples/project-structure.md) - Complete project structure details
- [Dio Setup](examples/dio-setup.md) - DioClient with interceptors
- [BLoC/Cubit Example](examples/bloc-cubit-example.md) - State management pattern
- [Repository Pattern](examples/repository-pattern.md) - Repository implementation
- [Remote DataSource Pattern](examples/remote-datasource-pattern.md) - Remote data source layer
- [Dependency Injection](examples/dependency-injection.md) - GetIt DI setup
- [Navigation Setup](examples/navigation-setup.md) - GoRouter configuration
- [Native Splash Setup](examples/native-splash-setup.md) - flutter_native_splash configuration
- [Freezed & JSON Serializable](examples/freezed-json-serializable.md) - Code generation for models
- [Build Runner Guide](examples/build-runner-guide.md) - Build runner commands and tips

### Resources
- [pubspec Template](resources/pubspec-template.yaml) - Complete pubspec.yaml
- [Android Permissions](resources/android-permissions.md) - AndroidManifest setup
- [iOS Permissions](resources/ios-permissions.md) - Info.plist setup
- [Environment Setup](resources/environment-setup.md) - Multi-environment config
- [Security Checklist](resources/security-checklist.md) - Security best practices

## Best Practices

### Naming Conventions
- Files: `snake_case.dart`
- Classes: `PascalCase`
- Variables/Functions: `camelCase`

### Layer Dependencies
```
views → model_view → data/repositories → data/data_sources
                  ↘ core (utilities)
```

### State Management
- Use `Cubit` for simple state
- Use `BLoC` for complex event-driven state
- Always use `freezed` for state classes

### Error Handling
- Use `Either<Failure, Success>` from dartz
- Define specific `Failure` types
- Handle network errors gracefully

## Troubleshooting

| Issue | Solution | Reference |
|-------|----------|-----------|
| Token refresh loop | Check DioAuthInterceptor logic | [Dio Setup](examples/dio-setup.md) |
| State not updating | Ensure Equatable props are correct | [BLoC/Cubit Example](examples/bloc-cubit-example.md) |
| DI not found | Verify registration order | [Dependency Injection](examples/dependency-injection.md) |
| Build runner errors | Run `flutter clean && flutter pub get` | [Build Runner Guide](examples/build-runner-guide.md) |
| Part file not found | Check part directive matches filename | [Freezed Guide](examples/freezed-json-serializable.md) |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/funnyhust) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
