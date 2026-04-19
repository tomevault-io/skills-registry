---
name: release
description: Merge PR, tag release, and create GitHub release with changelog notes Use when this capability is needed.
metadata:
  author: corv89
---

# /release - Tag and Publish Release

## Purpose

Merge PR, tag release, and create GitHub release with changelog notes.

## Prerequisites

- PR exists and all CI checks pass
- You're ready to release (this is not reversible)

## Process

1. **Verify PR status**

   ```
   gh pr status
   gh pr checks
   ```

   Stop if checks are failing.

2. **Merge PR**

   ```
   gh pr merge --squash --delete-branch
   ```

3. **Pull main and verify version**

   ```
   git checkout main
   git pull
   ```

   Confirm version in pyproject.toml matches intended release.

4. **Create and push tag**

   ```
   git tag vX.Y.Z
   git push origin vX.Y.Z
   ```

5. **Create GitHub release**

   ```
   gh release create vX.Y.Z --title "vX.Y.Z" --notes-file <changelog-section>
   ```

   Extract the relevant section from CHANGELOG.md for release notes.
   Mark as prerelease if appropriate (`--prerelease`).

## Notes

- CI typically handles PyPI publishing on tag push
- If release fails, delete the tag before retrying:
  ```
  git tag -d vX.Y.Z
  git push origin :refs/tags/vX.Y.Z
  ```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/corv89) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
