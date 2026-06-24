---
name: flutter
description: > Use when this capability is needed.
metadata:
  author: odjaramillo
---

## Critical Patterns

### Widget Structure (REQUIRED)

```dart
// ✅ ALWAYS: Prefer StatelessWidget when possible
class UserCard extends StatelessWidget {
  final User user;
  
  const UserCard({super.key, required this.user});
  
  @override
  Widget build(BuildContext context) {
    return Card(
      child: ListTile(
        title: Text(user.name),
        subtitle: Text(user.email),
      ),
    );
  }
}
```

### State Management (REQUIRED)

```dart
// ✅ Use Riverpod for state management
final userProvider = FutureProvider<User>((ref) async {
  return await api.getUser();
});

// In widget
Consumer(
  builder: (context, ref, child) {
    final userAsync = ref.watch(userProvider);
    return userAsync.when(
      data: (user) => Text(user.name),
      loading: () => CircularProgressIndicator(),
      error: (e, s) => Text('Error: $e'),
    );
  },
)
```

### Null Safety (REQUIRED)

```dart
// ✅ ALWAYS: Use null safety properly
String? nullableName;
String nonNullName = nullableName ?? 'Default';

// Use late for lazy initialization
late final UserService userService;
```

---

## Decision Tree

```
Need simple UI?            → StatelessWidget
Need local state?          → StatefulWidget
Need shared state?         → Provider/Riverpod
Need navigation?           → GoRouter
Need forms?                → Form + TextFormField
```

---

## Commands

```bash
flutter create myapp
flutter run
flutter build apk
flutter build ios
flutter test
flutter analyze
```

---
> Source: [odjaramillo/custom-rules](https://github.com/odjaramillo/custom-rules) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
