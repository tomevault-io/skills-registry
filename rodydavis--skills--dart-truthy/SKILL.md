---
name: check-if-an-object-is-truthy-in-dart
description: Learn how to extend Dart's functionality to implement JavaScript-style "truthy" checks for easier conditional logic and value evaluations. Use when this capability is needed.
metadata:
  author: rodydavis
---

# Check if an Object is Truthy in Dart


If you are coming from language like JavaScript you may be used to checking if an object is [truthy](https://developer.mozilla.org/en-US/docs/Glossary/Truthy).

```
if (true)
if ({})
if ([])
if (42)
if ("0")
if ("false")
if (new Date())
if (-42)
if (12n)
if (3.14)
if (-3.14)
if (Infinity)
if (-Infinity)
```

In Dart you need to explicitly check if an object is not null, true/false or determine if the value is true based on the type.

It is possible however to use Dart extensions to add the truthy capability.

```
extension on Object? {
  bool get isTruthy => truthy(this);
}

bool truthy(Object? val) {
  if (val == null) return false;
  if (val is bool) return val;
  if (val is num && val == 0) return false;
  if (val is String && (val == 'false' || val == '')) return false;
  if (val is Iterable && val.isEmpty) return false;
  if (val is Map && val.isEmpty) return false;
  return true;
}
```

This will now make it possible for any object to be evaluated as a truthy value in if statements or value assignments.

Prints the following:

```
(null, false)
(, false)
(false, false)
(true, true)
(0, false)
(1, true)
(false, false)
(true, true)
([], false)
([1, 2, 3], true)
({}, false)
({1, 2, 3}, true)
({a: 1, b: 2}, true)
```

## Demo

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/rodydavis) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
