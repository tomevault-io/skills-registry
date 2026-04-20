---
name: clean-architecture-scaffolder
description: Generates the folder structure and boilerplate code for a new feature using Clean Architecture, BLoC, and Freezed.
metadata:
  author: tsiresymila1
---

# Clean Architecture Scaffolder

Use this skill when the user asks to "create a new feature" or "scaffold a feature".

## 1. Directory Structure
Create the following directory structure under `lib/features/<feature_name>/`:

```
lib/features/<feature_name>/
├── data/
│   ├── datasources/
│   │   └── <feature_name>_remote_data_source.dart
│   ├── models/
│   │   └── <feature_name>_model.dart
│   └── repositories/
│       └── <feature_name>_repository_impl.dart
├── domain/
│   ├── entities/
│   │   └── <feature_name>_entity.dart
│   ├── repositories/
│   │   └── <feature_name>_repository.dart
│   └── usecases/
│       └── get_<feature_name>_usecase.dart
└── presentation/
    ├── bloc/
    │   ├── <feature_name>_bloc.dart
    │   ├── <feature_name>_event.dart
    │   └── <feature_name>_state.dart
    ├── pages/
    │   └── <feature_name>_page.dart
    └── widgets/
```

## 2. Implementation Details

### Domain Layer
- **Entity**: Create a simple immutable class.
- **Repository Interface**: Define abstract methods returning `Future<Either<Failure, Type>>` (using `dartz` if applicable, or just `Future`).
- **UseCase**: Implement a callable class that executes a specific business rule.

### Data Layer
- **Model**: Create a `Freezed` data class with `fromJson`. It should probably extend or map to the Entity.
  ```dart
  @freezed
  class FeatureModel with _$FeatureModel {
    const factory FeatureModel(...) = _FeatureModel;
    factory FeatureModel.fromJson(Map<String, dynamic> json) => _$FeatureModelFromJson(json);
  }
  ```
- **RemoteDataSource**: Interface and implementation for API calls (using Dio).
- **Repository Implementation**: Implements the Domain Repository, calls DataSource, and handles exceptions.

### Presentation Layer
- **Bloc**: Use `flutter_bloc` and `freezed` for Events and States.
  ```dart
  @freezed
  class FeatureEvent with _$FeatureEvent { ... }
  
  @freezed
  class FeatureState with _$FeatureState { ... }
  ```
- **Page**: A `StatelessWidget` that provides the Bloc (using `BlocProvider`) and builds the UI.

## 3. Service Locator
- Automatically register the new Data Source, Repository, UseCase, and Bloc in `lib/service_locator.dart` using `get_it`.

## 4. Post-Action
- Run `dart run build_runner build --delete-conflicting-outputs` to generate Freezed and JSON files.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/tsiresymila1) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
