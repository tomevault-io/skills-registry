---
name: dev-flutter-patterns
description: Common Flutter specific development patterns. Triggers: flutter, mobile, app, riverpod Use when this capability is needed.
metadata:
  author: aloisdeniel
---

# Flutter Development Patterns

This skill provides common Flutter development patterns to be used when developping Flutter apps with Riverpod.

## Use `const` widgets instances whenever possible

```dart
return const Padding(
  padding: EdgeInsets.all(8.0),
  child: Icon(Icons.star),
);
```

## Provide unique keys to widget instances in lists

```dart
ListView.builder(
  itemCount: items.length,
  itemBuilder: (context, index) {
    final item = items[index];
    return Tile(
      key: ValueKey(item.id), // Unique key based on item ID
      data: item,
    );
  },
);
```

## Always prefix provider declarations with `$`

```dart
final $example = Provider<Example>((ref) {
  return Example();
});
```


## For providers which needs initialization, create two providers: `$load<Name>` and `$name`

```dart
final $loadExample = FutureProvider<Example>((ref) async {
  // Async initialization logic
});

final $example = Provider<Example>((ref) {
  final asyncValue = ref.watch($loadExample);
  return switch (asyncValue) {
    AsyncData(value) => value,
    _ => throw Exception('Example is not initialized'),
  }
```

## Use `AsyncValue` for asynchronous loading states

```dart
class ExampleNotifier extends AsyncNotifier<State> {
  @override
  Future<State> build() async {
    // Initial loading state
    return fetchData();
  }

  Future<void> refresh() async {
    // Set loading state
    state = const AsyncValue.loading();
    try {
      final data = await fetchData();
      // Set data state
      state = AsyncValue.data(data);
    } catch (e, st) {
      // Set error state
      state = AsyncValue.error(e, st);
    }
  }
}
```

## NEVER put hardcoded strings in the UI, create a `ConsumerWidget` and watch `$l10n` instead

```dart
class Example extends ConsumerWidget {
  @override
  Widget build(BuildContext context, WidgetRef ref) {
    final l10n = ref.watch($l10n);
    return Text(l10n.exampleHelloWorld);
  }
}
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/aloisdeniel) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
