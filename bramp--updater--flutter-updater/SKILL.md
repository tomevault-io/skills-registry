---
name: flutter-updater
description: Specialized skill for updating Flutter/Dart packages, adhering to the standards defined in project-updater. Use when this capability is needed.
metadata:
  author: bramp
---

# Flutter Package Updater

This skill specializes in updating Flutter/Dart packages, adhering to the standard philosophy in `project-updater/SKILL.md`.

## Language Standards

1.  **Workspaces**: Adopt Dart/Flutter workspaces (requires SDK ^3.5.0) for multi-package projects.
    - **Migration from Melos**: If migrating from Melos:
        - Add `workspace` and `resolution: workspace` to `pubspec.yaml`.
        - Create a `Makefile` that replicates all Melos scripts.
        - Remove `melos.yaml` and `melos` from dev_dependencies.
2.  **Linting**: Prefer `package:very_good_analysis` for the strictest modern linting rules.
3.  **Formatting**: Always use `dart format`.
4.  **Pub.dev Standards**: Ensure `CHANGELOG.md` is updated and `publish_to` is commented out (to prevent accidental local publishing while still signaling intent).

## Standard Makefile (Flutter)

A `Makefile` for a Flutter project should implement the standard targets:
- `all`: `format analyze test`
- `format`: `dart format .`
- `analyze`: `dart analyze`
- `test`: `dart test` (or `flutter test`)
- `test-ci`: `dart test --reporter=compact`
- `fix`: `dart fix --apply`
- `upgrade`: `dart pub upgrade --major-versions --tighten`

## GitHub Actions (Flutter)

The `test.yml` for Flutter should:
- Use `subosito/flutter-action@v2`.
- Cache the `.pub-cache`.
- Run `make test-ci`.

Also ensure `.github/dependabot.yml` is present to keep dependencies updated, including the standard cooldown settings:
```yaml
    cooldown:
      default-days: 7      # The baseline safety net
      semver-major-days: 30 # Wait for the community to find breaking changes
      semver-minor-days: 14 # Balance between new features and stability
      semver-patch-days: 3  # Short wait to ensure a patch isn't immediately yanked
```

## Dependency Management

- Use `dart pub outdated` to identify major version upgrades.
- Tighten SDK constraints to the latest stable release.

---
> Source: [bramp/updater](https://github.com/bramp/updater) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
