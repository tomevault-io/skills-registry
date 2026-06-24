---
name: flutter-dispose-discipline
description: Ensure every Flutter controller and subscription is properly disposed. Use when reviewing StatefulWidget code, when user reports memory leaks, or when creating widgets with controllers, streams, or animation controllers. Use when this capability is needed.
metadata:
  author: thawzintoe-ptut
---

# Flutter Dispose Discipline Skill

Every resource that requires cleanup MUST be disposed. No exceptions.

## When to apply
- Creating a StatefulWidget with any controller
- Code review of StatefulWidget code
- Memory leak investigation
- User reports "my app gets slower over time"

## Resources that MUST be disposed

| Resource | Created in | Disposed in |
|----------|-----------|-------------|
| `TextEditingController` | `initState` or field | `dispose()` |
| `ScrollController` | `initState` or field | `dispose()` |
| `AnimationController` | `initState` | `dispose()` |
| `FocusNode` | `initState` or field | `dispose()` |
| `TabController` | `initState` | `dispose()` |
| `PageController` | `initState` or field | `dispose()` |
| `StreamSubscription` | `initState` | `cancel()` in `dispose()` |
| `Timer` | anywhere | `cancel()` in `dispose()` |
| `Worker` (GetX) | `onInit` | `dispose()` / `onClose()` |
| `Debouncer` | field | `dispose()` |

## Correct pattern
```dart
class MyWidget extends StatefulWidget { ... }

class _MyWidgetState extends State<MyWidget> {
  late final TextEditingController _nameController;
  late final ScrollController _scrollController;
  StreamSubscription<Event>? _subscription;

  @override
  void initState() {
    super.initState();
    _nameController = TextEditingController();
    _scrollController = ScrollController();
    _subscription = eventStream.listen(_onEvent);
  }

  @override
  void dispose() {
    _subscription?.cancel();
    _scrollController.dispose();
    _nameController.dispose();
    super.dispose();  // ALWAYS last
  }
}
```

## Common mistakes

### Forgetting dispose
```dart
// BAD — controller leaks
class _State extends State<MyWidget> {
  final controller = TextEditingController();
  // NO dispose() override!
}
```

### Disposing in wrong order
```dart
// BAD — super.dispose() called first
@override
void dispose() {
  super.dispose();  // WRONG — should be last
  _controller.dispose();
}
```

### Using controller after dispose
```dart
// BAD — accessing controller in async callback after dispose
Future<void> _save() async {
  await repository.save(_controller.text); // might be disposed
  // FIX: check mounted before accessing controller
  if (!mounted) return;
  _controller.clear();
}
```

## Flutter Hooks alternative
With `flutter_hooks`, disposal is automatic:
```dart
class MyWidget extends HookWidget {
  @override
  Widget build(BuildContext context) {
    final controller = useTextEditingController(); // auto-disposed
    final scrollController = useScrollController(); // auto-disposed
    // ...
  }
}
```

## Checklist
- [ ] Every controller has a matching `dispose()` call
- [ ] Every `StreamSubscription` has `cancel()` in `dispose()`
- [ ] `super.dispose()` is the LAST call in `dispose()`
- [ ] No controller access after potential dispose (async gaps)
- [ ] `Timer` instances are cancelled in `dispose()`
- [ ] AnimationController uses `vsync: this` with `TickerProviderStateMixin`

---
> Source: [thawzintoe-ptut/Ptut-claude](https://github.com/thawzintoe-ptut/Ptut-claude) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-22 -->
