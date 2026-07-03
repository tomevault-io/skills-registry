---
name: migration
description: >- Use when this capability is needed.
metadata:
  author: fedify-dev
---

Help the user migrate Fedify code from “$ARGUMENTS”.


Migration workflow
------------------

1.  Fetch *CHANGES.md* from the repo to identify breaking changes between
    the versions in question:
    `https://raw.githubusercontent.com/fedify-dev/fedify/main/CHANGES.md`
2.  List every breaking change that affects the user's code range.
3.  For each breaking change, show the old API, the new API, and a concrete
    before/after code snippet.
4.  Search the user's codebase for usages of deprecated symbols and suggest
    the replacement.
5.  Note any dependency changes (e.g., vocabulary moved to `@fedify/vocab`,
    runtime to `@fedify/vocab-runtime`).


Key migration hints
-------------------

 -  `@fedify/fedify/vocab` → `@fedify/vocab` (dedicated package)
 -  `@fedify/fedify/runtime` → `@fedify/vocab-runtime`
 -  In-tree *src/webfinger* → `@fedify/webfinger`
 -  *src/x/* exports removed in 2.0.0

---
> Source: [fedify-dev/fedify](https://github.com/fedify-dev/fedify) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-07-03 -->
