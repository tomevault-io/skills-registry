---
name: publish
description: Publish the package to pub.dev. Runs dry-run first, then publishes on confirmation. Use when this capability is needed.
metadata:
  author: QuisApp
---

Publish the package to pub.dev.

## Steps

1. Run `flutter pub publish --dry-run` and show the output.
2. If `$ARGUMENTS` is "dry-run", stop here.
3. Otherwise, ask the user to confirm before proceeding.
4. On confirmation, run `flutter pub publish --force` (non-interactive).
5. Report success or failure.

---
> Source: [QuisApp/flutter_contacts](https://github.com/QuisApp/flutter_contacts) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-07-07 -->
