---
name: flutter
description: Coding conventions for Flutter and Dart development in this project. Use when this capability is needed.
metadata:
  author: jamesoidian
---

# Flutter & Dart Conventions

## General Principles
- **Clean & Explicit**: Write readable and maintainable Dart code.
- **Safety First**: Avoid `any` types; prefer strict typing and sound null safety.
- **Consistency**: Follow existing patterns in the codebase for widgets and state management.

## Widget Guidelines
- Use `const` constructors whenever possible to improve performance.
- Favor composition over inheritance.
- Large widget trees should be split into smaller, focused widgets.
- Prefer `StatelessWidget` unless internal state is absolutely necessary.

## Localization
- Use the localized strings from the defined `l10n` setup.
- Do not hardcode user-facing strings in widget trees.

## Testing
- Ensure new features have corresponding unit or widget tests when applicable.
- Run tests regularly using `flutter test`.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jamesoidian) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
