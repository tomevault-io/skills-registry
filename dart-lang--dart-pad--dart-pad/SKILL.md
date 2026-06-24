---
name: update-packages
description: >- Use when this capability is needed.
metadata:
  author: dart-lang
---

# Update Packages

## Step 1: Update allowlist (if needed)

If adding or removing packages, edit `lib/src/project_templates.dart` first.
This file contains the allowlist of supported packages. Skip this step if only
updating versions.

## Step 2: Update dependencies for each channel

Run the following for each channel (stable, beta, main):

```bash
flutter channel <CHANNEL>
flutter upgrade
dart tool/grind.dart update-pub-dependencies
```

## Step 3: Switch back to stable

```bash
flutter channel stable
```

## Step 4: Verify changes

Check `git status` and `git diff` to confirm the expected changes to
`tool/dependencies/pub_dependencies_stable.json`, `pub_dependencies_beta.json`,
and `pub_dependencies_main.json`.

---
> Source: [dart-lang/dart-pad](https://github.com/dart-lang/dart-pad) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-18 -->
