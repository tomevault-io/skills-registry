---
name: flutter-rebuild-audit
description: Audit a Flutter widget tree for unnecessary rebuilds. Use when user reports janky scrolling in Flutter, mentions performance, asks "why is this rebuilding," or after [PERF] mode flags rebuild count. Use when this capability is needed.
metadata:
  author: thawzintoe-ptut
---

# Flutter Rebuild Audit Skill

Systematically narrow rebuild scope in Flutter widget trees.

## When to apply
- Flutter DevTools rebuild stats show count > 10 on a stable screen
- User pastes widget code and asks why it rebuilds
- After `[PERF]` mode flags a rebuild hotspot
- Scroll jank in a ListView

## Diagnostic checklist (run in order)

### 1. const constructors
Every leaf widget that takes no dynamic data should be `const`:
```dart
// BAD — rebuilds every time parent rebuilds
SizedBox(height: 16)
Text('Static Title')

// GOOD — const prevents rebuild
const SizedBox(height: 16)
const Text('Static Title')
```

The Dart analyzer flags missing `const` — run `dart analyze` and fix all `prefer_const_constructors` warnings.

### 2. Provider/Riverpod ref scope
`ref.watch` rebuilds the ENTIRE widget. Narrow with `.select`:
```dart
// BAD — rebuilds on ANY state change
final state = ref.watch(settingsProvider);
// only uses state.theme

// GOOD — rebuilds only when theme changes
final theme = ref.watch(settingsProvider.select((s) => s.theme));
```

### 3. BLoC buildWhen
`BlocBuilder` rebuilds on every state emission. Use `buildWhen` to narrow:
```dart
// BAD — rebuilds on every state change
BlocBuilder<CartBloc, CartState>(
  builder: (context, state) => Text('${state.itemCount}'),
)

// GOOD — rebuilds only when itemCount changes
BlocBuilder<CartBloc, CartState>(
  buildWhen: (prev, curr) => prev.itemCount != curr.itemCount,
  builder: (context, state) => Text('${state.itemCount}'),
)
```

### 4. List keys
`ListView.builder` items need stable keys when reorderable:
```dart
ListView.builder(
  itemCount: items.length,
  itemBuilder: (context, index) => ItemTile(
    key: ValueKey(items[index].id),  // stable key
    item: items[index],
  ),
)
```

### 5. ValueListenableBuilder granularity
Split listenables when sub-trees rebuild for unrelated changes:
```dart
// BAD — one ValueNotifier for everything
final appState = ValueNotifier(AppState(...));

// GOOD — separate notifiers for independent data
final cartCount = ValueNotifier(0);
final userName = ValueNotifier('');
```

### 6. MediaQuery access
`MediaQuery.of(context)` causes rebuild on ANY media change. Use specific methods:
```dart
// BAD — rebuilds on keyboard, orientation, padding changes
final size = MediaQuery.of(context).size;

// GOOD — rebuilds only on size changes
final size = MediaQuery.sizeOf(context);
```

## Severity tags
- **Blocker** — O(n) rebuilds per frame (e.g., ref.watch without select on a list screen)
- **Major** — measurable jank (missing const on hot-path widgets, broad BlocBuilder)
- **Minor** — best practice (MediaQuery.of -> MediaQuery.sizeOf)

## Anti-patterns to flag
- Wrapping entire screen in a single `setState` widget
- `ref.watch` of a deep object without `.select`
- Missing `const` on leaf widgets in a ListView
- `Builder` widget used as "performance fix" without actual scope reduction
- `setState` called in `build`
- Creating objects inside `build` that could be `const` or cached

---
> Source: [thawzintoe-ptut/Ptut-claude](https://github.com/thawzintoe-ptut/Ptut-claude) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-22 -->
