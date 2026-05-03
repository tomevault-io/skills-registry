---
name: release
description: Prepare a pub.dev release with version bump, changelog update, and publish dry-run Use when this capability is needed.
metadata:
  author: jhosm
---

# Release Workflow

Prepare a new release of the dart_acdc package for pub.dev.

## Steps

1. Read current version from `pubspec.yaml`
2. Ask user for version bump type (patch/minor/major) or a specific version
3. Update version in `pubspec.yaml`
4. Update `CHANGELOG.md`:
   - Check git log since last tag for unreleased changes
   - Add a new version section with today's date
   - Categorize changes (Added, Changed, Fixed, Removed)
5. Run `make ci` to validate everything passes
6. Run `dart pub publish --dry-run` to verify the package is publishable
7. Create commit: `chore: release vX.Y.Z`
8. Create git tag: `vX.Y.Z`
9. Report what was done and remind user to `git push --tags` when ready

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jhosm) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
