---
name: run-codegen
description: Run Flutter build_runner to regenerate code (freezed, json_serializable) Use when this capability is needed.
metadata:
  author: shirokun20
---

# Run Codegen

This skill runs the Flutter build_runner command to regenerate code. This is necessary when you modify models annotated with @freezed or @JsonSerializable.

```bash
echo "Running build_runner..."
flutter pub run build_runner build --delete-conflicting-outputs
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/shirokun20) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
