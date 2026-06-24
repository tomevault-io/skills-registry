---
name: dev-dart-patterns
description: Common Dart specific development patterns. Triggers: flutter, dart Use when this capability is needed.
metadata:
  author: aloisdeniel
---

# Dart Development Patterns

This skill provides common development patterns to be used when developping programs using the Dart programming language.

## In class declarations, put the constructors first, then the properties, then the methods

```dart
class Example {
  const Example({
    required this.name,
  });

  final String name;

  void greet() {
    print('Hello, $name!');
  }
}
```

## Use sealed classes for restricted class hierarchies

```dart
/// Define a sealed class with subclasses
sealed class Result<T> {
  const Result();

  const factory Result.success(T data) = Success<T>;

  const factory Result.error(int code) = Error<T>;
}

class Success<T> extends Result<T> {
  final T data;
  const Success(this.data);
}

class Error<T> extends Result<T> {
  final int code;
  const Error(this.code);
}
```

## Prefer `async` functions with `await` and try/catch blocks over chaining `then` calls

```dart
Future<String> fetchData() async {
  try {
    final response = await http.get(Uri.parse('https://api.example.com/data'));
    if (response.statusCode == 200) {
      return response.body;
    } else {
      throw FetchException('Failed to load data, statuc code: ${response.statusCode}', inner: e);
    }
  } catch (e) {
    throw FetchException('Error fetching data', inner: e);
  }
}
```

## User pattern matching with `switch` statements and expressions when possible

```dart
/// Pattern matching expressions with `switch`
@override
Widget build(BuildContext context) {
    return switch(result) {
        Success(:final data) => Container(color: Colors.green, child: Text('Success with data: $data')),
        Error(:final code) => Text('Error with code: $code'),
    };
}

/// Pattern matching statements with `switch`
switch(result) {
    case Success(:final data):
    print('Success with data: $data');
    case Error(:final code)
    print('Error with code: $code');
}
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/aloisdeniel) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
