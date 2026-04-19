---
name: git-push
description: Quick git add, commit and push with auto-generated message Use when this capability is needed.
metadata:
  author: tooyakov-art
---

# Quick Git Push

Quickly commit and push all changes:

1. Remove any problematic files (like Windows 'nul')
2. Stage all changes with `git add -A`
3. Create commit with provided message or auto-generate one
4. Push to origin

```bash
rm -f nul 2>/dev/null
git add -A
git commit -m "$ARGUMENTS"
git push
```

If no message provided ($ARGUMENTS is empty), generate a brief commit message based on the changed files.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/tooyakov-art) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
