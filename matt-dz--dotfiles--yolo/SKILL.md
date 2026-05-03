---
name: yolo
description: Add, commit, and push everything in the git repository. Use when this capability is needed.
metadata:
  author: matt-dz
---
Add, commit, and push everything in the git repository.

1. Use `git add -A` to add all files.
2. Use `git commit -m <message>`. The message should use git conventional commits and should be short and sweet. Don't add any co-authoring or make the commit messages super long - messages should be short and sweet.
3. Use `git push` to push the changes. If this fails, you are either not in a git repository, in a detached state, the branch is not connected to the upstream, or something else. Investigate the situation, if it is not possible to push do not force it. Explain the issue to the user and do not take action unless told.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/matt-dz) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
