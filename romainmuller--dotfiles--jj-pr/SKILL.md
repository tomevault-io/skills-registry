---
name: jj-pr
description: |- Use when this capability is needed.
metadata:
  author: romainmuller
---

# Workflow

1. If needed, create a new bookmark using `jj bookmark create <BOOKMARK_NAME>` using a short, descriptive
   `<BOOKMARK_NAME>` according to the user's preference or instructions
2. Push the `<BOOKMARK_NAME>` to GitHub using `jj git push -r <BOOKMARK_NAME>`
3. Create the PR on GitHub using `gh pr create --head <BOOKMARK_NAME>`, adding `--draft` if the user requested a draft
   Pull Request
4. Return the URL to the PR to the user.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/romainmuller) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
