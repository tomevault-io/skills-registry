---
name: validate-pr
description: >- Use when this capability is needed.
metadata:
  author: flutter
---

# Stage the Flutter site

Before committing changes or reviewing a PR locally,
it is important to stage the site and ensure everything works correctly. 
Follow these steps to stage the site:

## 1. Sync code excerpts

Ensure that any changed code excerpts are properly run and synced to the
Markdown files:

```bash
dart run dash_site refresh-excerpts
```

## 2. Update formatting

Update the formatting of the site examples and tools to ensure
they follow the official Dart format:

```bash
dart run dash_site format-dart
```

## 3. Check for broken links

Run the following command to check for any unlinked or broken
Markdown link references in the site output:

```bash
dart run dash_site check-link-references
```

If any broken links are found, try to patch them or alert the user.

## 4. Stage the site locally

Finally, serve a local development server of the site so the user can preview the changes:

```bash
dart run dash_site serve
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/flutter) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
