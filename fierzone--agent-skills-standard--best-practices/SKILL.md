---
name: dart-best-practices
description: General purity standards for Dart development. Use when this capability is needed.
metadata:
  author: fierzone
---

# Dart Best Practices

## **Priority: P1 (OPERATIONAL)**

Best practices for writing clean, maintainable Dart code.

- **Scoping**:
  - No global variables.
  - Private globals (if required) must start with `_`.
- **Immutability**: Use `const` > `final` > `var`.
- **Config**: Use `--dart-define` for secrets. Never hardcode API keys.
- **Naming**: Follow [effective-dart](https://dart.dev/guides/language/effective-dart) (PascalCase classes, camelCase members).

```dart
import 'models/user.dart'; // Good
import 'package:app/models/user.dart'; // Avoid local absolute
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/fierzone) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
