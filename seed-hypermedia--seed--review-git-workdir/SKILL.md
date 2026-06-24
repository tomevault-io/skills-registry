---
name: review-git-workdir
description: Review Git working directory. Use when this capability is needed.
metadata:
  author: seed-hypermedia
---

Do the code review of the current state of the git working directory. Use `git status` command to see changed files.

- Ignore code generation artifacts like protobuf generated files, and similar.
- Ignore files with `.gensum` file extension.
- Pay attention to any left out debug statements, code that's left commented out, unused code, and any other potential issues.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/seed-hypermedia) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
