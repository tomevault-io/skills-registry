---
name: dev-autodev
description: Common autodev loop for branch, implement, test, commit, and PR summary. Use when this capability is needed.
metadata:
  author: guan404ming
---

# Autodev Loop

Shared development workflow used by project-specific dev skills. No planning step, go straight to implementation.

## Steps

0. **Sync**, detect the default branch (`git remote show upstream 2>/dev/null | sed -n 's/.*HEAD branch: //p'`, fallback to `main`), checkout it and run `git sync`.

1. **Create a branch** for the feature/fix.

2. **Implement**, follow existing coding style, keep changes minimal.

3. **Add tests**, parameterized, concise, following project conventions.

4. **Verify**, run the project's build and test commands.

5. **Commit** with `git commit -n -m "msg"`, no co-author tags.

6. **PR summary:** Use `/dev-pr-summarize` to generate a changelog.

7. **Checkout the default branch** when done.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/guan404ming) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
