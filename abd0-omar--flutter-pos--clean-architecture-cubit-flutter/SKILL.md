---
name: clean-architecture-cubit-flutter
description: Flutter development with Cubit state management, Clean Architecture, go_router, mocktail testing, and design system enforcement Use when this capability is needed.
metadata:
  author: abd0-omar
---

# Flutter Skill — Cubit + Clean Architecture

> **Note:** This skill uses **Cubit only** (not full Bloc) for simplicity. Cubit is a lightweight
> subset of Bloc that uses functions instead of events, making it more AI-friendly and easier to understand.

---

## Decision Tree: Choosing Your Approach

```
User task -> What are they building?
  |
  |- New screen/feature -> Full feature implementation:
  |  1. Create feature folder (lib/presentation/features/[feature]/)
  |  2. Define Cubit + State (cubit/[feature]_cubit.dart, _state.dart)
  |  3. Create data layer (data/datasources/, data/repositories/, data/models/)
  |  4. Build UI ([feature]_screen.dart, widgets/)
  |  5. Register in injection.dart
  |
  |- New widget only -> Presentation layer:
  |  1. Feature-specific: presentation/features/[feature]/widgets/
  |  2. Shared/reusable: presentation/common/
  |  3. Use design system constants (no hardcoded values)
  |  4. Connect to existing Cubit if needed
  |
  |- Data integration -> Data layer only:
  |  1. Create datasource (data/datasources/)
  |  2. Create repository (data/repositories/)
  |  3. Add use case (domain/usecases/)
  |  4. Wire up in existing or new Cubit
  |
  '- Refactoring -> Identify violations:
     1. Check for hardcoded colors/spacing/typography
     2. Check for business logic in UI
     3. Check for direct SDK calls outside datasources
     4. Check for missing Loading state before async operations
     5. Check for improper error handling (use SnackBar + AppColors.error)
```

---

## Project Structure

```
project/
├── lib/
│   ├── core/                           # Core utilities
│   │   ├── constants/                  # App-wide constants (AppColors, AppSpacing, etc.)
│   │   ├── errors/                     # Custom exceptions & failures
│   │   │   ├── exceptions.dart
│   │   │   └── failures.dart
│   │   ├── extensions/                 # Dart extensions
│   │   ├── router/                     # go_router configuration
│   │   │   └── app_router.dart
│   │   ├── theme/                      # App theme
│   │   │   └── app_theme.dart
│   │   └── utils/                      # Utility functions
│   │
│   ├── data/                           # Data layer
│   │   ├── datasources/                # Remote & local data sources
│   │   │   ├── remote/
│   │   │   └── local/
│   │   ├── models/                     # Data models (with JSON serialization)
│   │   └── repositories/               # Repository implementations
│   │
│   ├── domain/                         # Domain layer (business logic)
│   │   ├── entities/                   # Business entities (pure Dart)
│   │   ├── repositories/               # Repository interfaces (abstract)
│   │   └── usecases/                   # Use cases (single responsibility)
│   │
│   ├── presentation/                   # UI layer
│   │   ├── common/                     # Shared widgets
│   │   └── features/                   # Feature modules
│   │       └── feature_name/
│   │           ├── cubit/              # Cubit + States
│   │           │   ├── feature_cubit.dart
│   │           │   └── feature_state.dart
│   │           ├── widgets/            # Feature-specific widgets
│   │           └── feature_screen.dart
│   │
│   ├── injection.dart                  # Dependency injection setup
│   ├── app.dart                        # MaterialApp configuration
│   └── main.dart                       # Entry point
│
├── test/
│   ├── unit/                           # Unit tests
│   ├── widget/                         # Widget tests
│   └── integration/                    # Integration tests
│
├── pubspec.yaml
└── analysis_options.yaml
```

### Key Rules

- All state changes flow through Cubit
- No direct backend SDK calls outside datasources
- Zero hardcoded values (colors, spacing, typography)
- Repository pattern for all data access
- Use cases contain business logic, not Cubits
- Always show Loading before async work

---

## Cubit State Management

> Cubit is part of the `flutter_bloc` package but simpler than full Bloc.
> Instead of events, you call methods directly. Instead of `mapEventToState`, you just `emit()`.

### State Definition (Freezed)
```dart
// lib/presentation/features/users/cubit/users_state.dart
import 'package:freezed_annotation/freezed_annotation.dart';
import '../../../../domain/entities/user.dart';

part 'users_state.freezed.dart';

@freezed
sealed class UsersState with _$UsersState {
  const factory UsersState.initial() = UsersInitial;
  const factory UsersState.loading() = UsersLoading;
  const factory UsersState.loaded(List<User> users) = UsersLoaded;
  const factory UsersState.error(String message) = UsersError;
}
```

### Cubit Definition
```dart
// lib/presentation/features/users/cubit/users_cubit.dart
import 'package:flutter_bloc/flutter_bloc.dart';
import '../../../../domain/usecases/get_users.dart';
import 'users_state.dart';

class UsersCubit extends Cubit<UsersState> {
  UsersCubit({required this.getUsers}) : super(const UsersState.initial());

  final GetUsers getUsers;

  Future<void> loadUsers() async {
    emit(const UsersState.loading());

    final result = await getUsers();

    result.fold(
      (failure) => emit(UsersState.error(failure.message)),
      (users) => emit(UsersState.loaded(users)),
    );
  }

  Future<void> refresh() async {
    await loadUsers();
  }
}
```

### Using Cubit in UI
```dart
// lib/presentation/features/users/users_screen.dart
import 'package:flutter/material.dart';
import 'package:flutter_bloc/flutter_bloc.dart';
import 'cubit/users_cubit.dart';
import 'cubit/users_state.dart';

class UsersScreen extends StatelessWidget {
  const UsersScreen({super.key});

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: const Text('Users')),
      body: BlocBuilder<UsersCubit, UsersState>(
        builder: (context, state) {
          return switch (state) {
            UsersInitial() => const Center(child: Text('Press load')),
            UsersLoading() => const Center(child: CircularProgressIndicator()),
            UsersLoaded(:final users) => UsersList(users: users),
            UsersError(:final message) => ErrorDisplay(
                message: message,
                onRetry: () => context.read<UsersCubit>().loadUsers(),
              ),
          };
        },
      ),
      floatingActionButton: FloatingActionButton(
        onPressed: () => context.read<UsersCubit>().loadUsers(),
        child: const Icon(Icons.refresh),
      ),
    );
  }
}
```

### BlocListener for Side Effects

> Use `BlocListener` (it works with Cubit too) to react to state changes without rebuilding UI.
```dart
BlocListener<AuthCubit, AuthState>(
  listener: (context, state) {
    if (state case AuthError(:final message)) {
      ScaffoldMessenger.of(context).showSnackBar(
        SnackBar(
          content: Text(message),
          backgroundColor: AppColors.error,
        ),
      );
    }
    if (state case AuthAuthenticated()) {
      context.go('/home');
    }
  },
  child: const LoginForm(),
)
```

### BlocConsumer (Listener + Builder)

> `BlocConsumer` works with Cubit — use it when you need both listener and builder.
```dart
BlocConsumer<LoginCubit, LoginState>(
  listener: (context, state) {
    if (state case LoginSuccess()) {
      context.go('/home');
    }
  },
  builder: (context, state) {
    final isLoading = state is LoginLoading;
    return LoginButton(
      onPressed: isLoading ? null : () => context.read<LoginCubit>().login(),
      isLoading: isLoading,
    );
  },
)
```

---

## Clean Architecture Layers

### Data Flow
```
UI -> Cubit (emit Loading) -> Use Case -> Repository -> Datasource (SDK)
  -> Repository (map to entity) -> Cubit (Success/Error) -> UI
```

### Entity (Domain Layer)
```dart
// lib/domain/entities/user.dart
class User {
  const User({
    required this.id,
    required this.name,
    required this.email,
  });

  final String id;
  final String name;
  final String email;
}
```

### Model (Data Layer)
```dart
// lib/data/models/user_model.dart
import '../../domain/entities/user.dart';

class UserModel extends User {
  const UserModel({
    required super.id,
    required super.name,
    required super.email,
  });

  factory UserModel.fromJson(Map<String, dynamic> json) {
    return UserModel(
      id: json['id'] as String,
      name: json['name'] as String,
      email: json['email'] as String,
    );
  }

  Map<String, dynamic> toJson() {
    return {
      'id': id,
      'name': name,
      'email': email,
    };
  }
}
```

### Repository Interface (Domain Layer)
```dart
// lib/domain/repositories/user_repository.dart
import 'package:dartz/dartz.dart';
import '../../core/errors/failures.dart';
import '../entities/user.dart';

abstract class UserRepository {
  Future<Either<Failure, List<User>>> getUsers();
  Future<Either<Failure, User>> getUserById(String id);
  Future<Either<Failure, void>> createUser(User user);
}
```

### Repository Implementation (Data Layer)
```dart
// lib/data/repositories/user_repository_impl.dart
import 'package:dartz/dartz.dart';
import '../../core/errors/exceptions.dart';
import '../../core/errors/failures.dart';
import '../../domain/entities/user.dart';
import '../../domain/repositories/user_repository.dart';
import '../datasources/remote/user_remote_datasource.dart';

class UserRepositoryImpl implements UserRepository {
  const UserRepositoryImpl({required this.remoteDataSource});

  final UserRemoteDataSource remoteDataSource;

  @override
  Future<Either<Failure, List<User>>> getUsers() async {
    try {
      final users = await remoteDataSource.getUsers();
      return Right(users);
    } on ServerException catch (e) {
      return Left(ServerFailure(message: e.message));
    }
  }

  @override
  Future<Either<Failure, User>> getUserById(String id) async {
    try {
      final user = await remoteDataSource.getUserById(id);
      return Right(user);
    } on ServerException catch (e) {
      return Left(ServerFailure(message: e.message));
    }
  }

  @override
  Future<Either<Failure, void>> createUser(User user) async {
    try {
      await remoteDataSource.createUser(user);
      return const Right(null);
    } on ServerException catch (e) {
      return Left(ServerFailure(message: e.message));
    }
  }
}
```

### Use Case (Domain Layer)
```dart
// lib/domain/usecases/get_users.dart
import 'package:dartz/dartz.dart';
import '../../core/errors/failures.dart';
import '../entities/user.dart';
import '../repositories/user_repository.dart';

class GetUsers {
  const GetUsers({required this.repository});

  final UserRepository repository;

  Future<Either<Failure, List<User>>> call() {
    return repository.getUsers();
  }
}
```

### Errors & Failures
```dart
// lib/core/errors/exceptions.dart
class ServerException implements Exception {
  const ServerException({required this.message});
  final String message;
}

class CacheException implements Exception {
  const CacheException({required this.message});
  final String message;
}

// lib/core/errors/failures.dart
abstract class Failure {
  const Failure({required this.message});
  final String message;
}

class ServerFailure extends Failure {
  const ServerFailure({required super.message});
}

class CacheFailure extends Failure {
  const CacheFailure({required super.message});
}
```

---

## Dependency Injection (GetIt)

```dart
// lib/injection.dart
import 'package:get_it/get_it.dart';
import 'package:dio/dio.dart';

import 'data/datasources/remote/user_remote_datasource.dart';
import 'data/repositories/user_repository_impl.dart';
import 'domain/repositories/user_repository.dart';
import 'domain/usecases/get_users.dart';
import 'presentation/features/users/cubit/users_cubit.dart';

final sl = GetIt.instance;

Future<void> initDependencies() async {
  // External
  sl.registerLazySingleton<Dio>(() => Dio());

  // Data sources
  sl.registerLazySingleton<UserRemoteDataSource>(
    () => UserRemoteDataSourceImpl(dio: sl()),
  );

  // Repositories
  sl.registerLazySingleton<UserRepository>(
    () => UserRepositoryImpl(remoteDataSource: sl()),
  );

  // Use cases
  sl.registerLazySingleton(() => GetUsers(repository: sl()));

  // Cubits (factory = new instance each time)
  sl.registerFactory(() => UsersCubit(getUsers: sl()));
}
```

---

## go_router Navigation

```dart
// lib/core/router/app_router.dart
GoRouter createRouter(AuthCubit authCubit) {
  return GoRouter(
    initialLocation: '/',
    refreshListenable: authCubit,
    redirect: (context, state) {
      final isLoggedIn = authCubit.state is AuthAuthenticated;
      final isLoggingIn = state.matchedLocation == '/login';

      if (!isLoggedIn && !isLoggingIn) return '/login';
      if (isLoggedIn && isLoggingIn) return '/';
      return null;
    },
    routes: [
      GoRoute(
        path: '/',
        builder: (context, state) => BlocProvider(
          create: (_) => sl<UsersCubit>()..loadUsers(),
          child: const UsersScreen(),
        ),
        routes: [
          GoRoute(
            path: 'user/:id',
            builder: (context, state) {
              final id = state.pathParameters['id']!;
              return UserDetailScreen(userId: id);
            },
          ),
        ],
      ),
      GoRoute(
        path: '/login',
        builder: (context, state) => const LoginScreen(),
      ),
    ],
    errorBuilder: (context, state) => ErrorScreen(error: state.error),
  );
}
```

### Navigation Commands
```dart
context.go('/user/123');           // Navigate (replaces stack)
context.push('/user/123');         // Push (adds to stack)
context.pop();                     // Pop
context.pushReplacement('/home');  // Replace current
context.goNamed('user', pathParameters: {'id': '123'});  // Named routes
```

---

## Design System (Non-Negotiable)

### Colors
- Use `AppColors.primary`, `AppColors.error`, `AppColors.textPrimary`
- Do not use `Color(0xFF...)`, `Colors.blue`, inline hex values

### Spacing
- Use `AppSpacing.xs`, `AppSpacing.sm`, `AppSpacing.md`, `AppSpacing.lg`
- Do not use `EdgeInsets.all(16.0)` or hardcoded padding values

### Border Radius
- Use `AppRadius.sm`, `AppRadius.md`, `AppRadius.lg`, `AppRadius.xl`
- Do not use `BorderRadius.circular(12)` or inline values

### Typography
- Use `AppTypography.headlineLarge`, `AppTypography.bodyMedium`, `theme.textTheme.bodyMedium`
- Do not use inline `TextStyle(fontSize: 16)`

---

## Testing with Mocktail

### Unit Test (Cubit)
```dart
import 'package:bloc_test/bloc_test.dart';
import 'package:dartz/dartz.dart';
import 'package:flutter_test/flutter_test.dart';
import 'package:mocktail/mocktail.dart';

class MockGetUsers extends Mock implements GetUsers {}

void main() {
  late MockGetUsers mockGetUsers;
  late UsersCubit cubit;

  setUp(() {
    mockGetUsers = MockGetUsers();
    cubit = UsersCubit(getUsers: mockGetUsers);
  });

  tearDown(() => cubit.close());

  final users = [
    const User(id: '1', name: 'John', email: 'john@example.com'),
  ];

  blocTest<UsersCubit, UsersState>(
    'emits [loading, loaded] when loadUsers succeeds',
    build: () {
      when(() => mockGetUsers()).thenAnswer((_) async => Right(users));
      return cubit;
    },
    act: (cubit) => cubit.loadUsers(),
    expect: () => [
      const UsersState.loading(),
      UsersState.loaded(users),
    ],
  );

  blocTest<UsersCubit, UsersState>(
    'emits [loading, error] when loadUsers fails',
    build: () {
      when(() => mockGetUsers()).thenAnswer(
        (_) async => const Left(ServerFailure(message: 'Server error')),
      );
      return cubit;
    },
    act: (cubit) => cubit.loadUsers(),
    expect: () => [
      const UsersState.loading(),
      const UsersState.error('Server error'),
    ],
  );
}
```

### Widget Test
```dart
import 'package:bloc_test/bloc_test.dart';
import 'package:flutter/material.dart';
import 'package:flutter_bloc/flutter_bloc.dart';
import 'package:flutter_test/flutter_test.dart';
import 'package:mocktail/mocktail.dart';

class MockUsersCubit extends MockCubit<UsersState> implements UsersCubit {}

void main() {
  late MockUsersCubit mockCubit;

  setUp(() {
    mockCubit = MockUsersCubit();
  });

  testWidgets('shows loading indicator when loading', (tester) async {
    when(() => mockCubit.state).thenReturn(const UsersState.loading());

    await tester.pumpWidget(
      MaterialApp(
        home: BlocProvider<UsersCubit>.value(
          value: mockCubit,
          child: const UsersScreen(),
        ),
      ),
    );

    expect(find.byType(CircularProgressIndicator), findsOneWidget);
  });

  testWidgets('shows user list when loaded', (tester) async {
    final users = [const User(id: '1', name: 'John', email: 'john@test.com')];
    when(() => mockCubit.state).thenReturn(UsersState.loaded(users));

    await tester.pumpWidget(
      MaterialApp(
        home: BlocProvider<UsersCubit>.value(
          value: mockCubit,
          child: const UsersScreen(),
        ),
      ),
    );

    expect(find.text('John'), findsOneWidget);
  });
}
```

---

## Anti-Patterns to Avoid

- ❌ **Business logic in widgets** — Move to Cubits + Use Cases
- ❌ **Cubit without dispose** — Always close cubits when done
- ❌ **Direct repository calls from UI** — Use use cases as intermediary
- ❌ **Mutable state** — Use immutable state classes (Freezed)
- ❌ **Ignoring Either** — Always handle both Left (failure) and Right (success)
- ❌ **setState with Cubit** — Use `emit()` only
- ❌ **Accessing context after async** — Store values before await
- ❌ **God Cubits** — Keep cubits focused on single feature
- ❌ **Skipping loading states** — Always show loading feedback
- ❌ **Hardcoded dependencies** — Use DI for testability
- ❌ **Hardcoded colors/spacing/typography** — Use design system constants

---

## Checklist Before Submitting

- [ ] Cubit uses Loading -> Success/Error for async work
- [ ] Use cases handle business logic, Cubit coordinates
- [ ] Either is fully handled with `fold()`
- [ ] No SDK calls outside datasources
- [ ] No hardcoded colors/spacing/typography
- [ ] Errors show SnackBar with `AppColors.error`
- [ ] Code formatted with `dart format`
- [ ] Tests written for Cubit and key widgets

---

## Quick Commands

```bash
# Get dependencies
flutter pub get

# Generate Freezed code
dart run build_runner build --delete-conflicting-outputs

# Watch for changes (during development)
dart run build_runner watch --delete-conflicting-outputs

# Run tests
flutter test

# Run with coverage
flutter test --coverage

# Analyze code
flutter analyze
```

---

## pubspec.yaml Reference

```yaml
dependencies:
  flutter:
    sdk: flutter
  flutter_bloc: ^8.1.6       # State management
  dartz: ^0.10.1             # Either type
  go_router: ^14.0.0         # Navigation
  get_it: ^8.0.0             # Dependency injection
  dio: ^5.4.0                # Networking
  freezed_annotation: ^2.4.1 # Data models
  json_annotation: ^4.9.0    # JSON serialization

dev_dependencies:
  flutter_test:
    sdk: flutter
  bloc_test: ^9.1.7          # Cubit testing
  mocktail: ^1.0.4           # Mocking
  build_runner: ^2.4.8       # Code generation
  freezed: ^2.5.2            # Freezed codegen
  json_serializable: ^6.8.0  # JSON codegen
  flutter_lints: ^4.0.0      # Linting
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/abd0-omar) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
