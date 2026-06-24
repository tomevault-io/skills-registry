---
name: flutter-async-gap-context
description: Prevent BuildContext use across async gaps in Flutter. Use when reviewing code with await + BuildContext, when user gets "looking up deactivated widget" errors, or when creating async callbacks in widgets. Use when this capability is needed.
metadata:
  author: thawzintoe-ptut
---

# Flutter Async Gap Context Skill

Never use `BuildContext` after an `await` without checking `mounted` first.

## When to apply
- Widget code uses `BuildContext` after an `await`
- User gets "Looking up a deactivated widget's ancestor" error
- Code review finds `Navigator.of(context)` after async operation
- `ScaffoldMessenger.of(context)` used after await

## The problem
When a widget is unmounted during an async operation, its `BuildContext` becomes invalid:
```dart
// CRASH — context may be invalid after await
Future<void> _submit() async {
  await repository.save(data);
  Navigator.of(context).pop();        // BOOM if widget unmounted
  ScaffoldMessenger.of(context)       // BOOM
      .showSnackBar(SnackBar(content: Text('Saved')));
}
```

## The fix: mounted guard
```dart
// SAFE — check mounted after every await
Future<void> _submit() async {
  await repository.save(data);

  if (!context.mounted) return;       // guard BEFORE using context
  Navigator.of(context).pop();

  if (!context.mounted) return;       // guard again if more awaits follow
  ScaffoldMessenger.of(context)
      .showSnackBar(SnackBar(content: Text('Saved')));
}
```

## Rules
1. After EVERY `await`, check `context.mounted` before using `context`
2. One `mounted` check per async gap — if there are multiple awaits, check after each
3. `context.mounted` is available in Flutter 3.7+ (use `mounted` on State for older)
4. In `StatefulWidget`, use `if (!mounted) return;` (State's property)
5. In `StatelessWidget` callbacks, capture context before the gap:

```dart
// StatelessWidget pattern
ElevatedButton(
  onPressed: () async {
    final nav = Navigator.of(context);  // capture BEFORE await
    final messenger = ScaffoldMessenger.of(context);
    await repository.save(data);
    nav.pop();                          // safe — captured before await
    messenger.showSnackBar(...);        // safe
  },
)
```

## Common patterns

### showDialog after async
```dart
Future<void> _confirmDelete() async {
  final confirmed = await showDialog<bool>(...);
  if (confirmed != true) return;

  await repository.delete(item);

  if (!context.mounted) return;  // REQUIRED
  Navigator.of(context).pop();
}
```

### Multiple sequential awaits
```dart
Future<void> _process() async {
  final result = await step1();
  if (!context.mounted) return;

  await step2(result);
  if (!context.mounted) return;

  Navigator.of(context).pushReplacement(...);
}
```

### GoRouter navigation
```dart
Future<void> _login() async {
  await authService.login(email, password);
  if (!context.mounted) return;
  context.go('/home');  // GoRouter also needs mounted check
}
```

## Checklist
- [ ] Every `await` followed by `context` use has a `mounted` check between them
- [ ] `Navigator.of(context)` after async has guard
- [ ] `ScaffoldMessenger.of(context)` after async has guard
- [ ] `context.go()` / `context.push()` (GoRouter) after async has guard
- [ ] `Theme.of(context)` after async has guard
- [ ] `showDialog` / `showModalBottomSheet` after async has guard

---
> Source: [thawzintoe-ptut/Ptut-claude](https://github.com/thawzintoe-ptut/Ptut-claude) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-22 -->
