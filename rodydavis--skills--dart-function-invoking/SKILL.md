---
name: various-ways-to-invoke-functions-in-dart
description: Discover the surprising flexibility of calling Dart functions, including mixed positional and named arguments, the `.call` operator, and dynamic invocation with `Function.apply`. Use when this capability is needed.
metadata:
  author: rodydavis
---

# Various Ways to Invoke Functions in Dart


There are multiple ways to call a [Function](https://dart.dev/language/functions) in Dart.

The examples below will assume the following function:

```
void myFunction(int a, int b, {int? c, int? d}) {
  print((a, b, c, d));
}
```

But recently I learned that you can call a functions positional arguments in any order mixed with the named arguments. 🤯

```
myFunction(1, 2, c: 3, d: 4);
myFunction(1, c: 3, d: 4, 2);
myFunction(c: 3, d: 4, 1, 2);
myFunction(c: 3, 1, 2, d: 4);
```

In addition you can use the [`.call`](https://dart.dev/language/callable-objects) operator to invoke the function if you have a reference to it:

```
myFunction.call(1, 2, c: 3, d: 4);
```

You can also use [`Function.apply`](https://api.flutter.dev/flutter/dart-core/Function/apply.html) to dynamically invoke a function with a reference but it should be noted that it will effect js dart complication size and performance:

```
Function.apply(myFunction, [1, 2], {#c: 3, #d: 4});
```

All of these methods print the following:

```
(1, 2, 3, 4)
```

## Demo

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/rodydavis) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
