---
name: flutter-code-gen
description: Run Dart build_runner for code generation of Mappable classes and JSON serialization. Use after adding or modifying data model classes that require generated code. Use when this capability is needed.
metadata:
  author: dhruvanbhalara
---

# Code Generation Guidelines

- Generate code for:
  - Mappable classes
  - JSON serialization
  - Other generated code
- Use this command for code generation:
  `dart run build_runner build --delete-conflicting-outputs`
- Run code generation after:
  - Adding new Mappable classes
  - Modifying existing Mappable classes

---
> Source: [dhruvanbhalara/skills](https://github.com/dhruvanbhalara/skills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-22 -->
