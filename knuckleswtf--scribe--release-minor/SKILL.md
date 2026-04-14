---
name: release-minor
description: Automates the process of tagging a new minor release for Scribe by analyzing commit messages, updating the changelog, and creating a GitHub release.
metadata:
  author: knuckleswtf
---

# Release Minor Version

This skill automates the process of tagging a new minor release for Scribe.

## Workflow

1.  **Analyze Commits & Build Changelog**:
    -   Identify the last release tag (e.g., using `git tag --sort=-v:refname | head -n 1`).
    -   Get all commits from the last tag to `HEAD`.
    -   Analyze commit messages to categorize them into "Added", "Modified", "Fixed", or "Removed".
    -   Draft a new section for `CHANGELOG.md` following the existing format:
        ```markdown
        ## <New Version> (<Date>)
        ### Added
        - [Description] ([#PR](link))

        ### Modified
        - ...
        ```

2.  **Update Files**:
    -   Prepend the new section to the top of the release list in `CHANGELOG.md`.
    -   Update the `public const VERSION` in `src/Scribe.php` to the new version number.

3.  **Commit and Push**:
    -   Stage `CHANGELOG.md` and `src/Scribe.php`.
    -   Commit with the message: `Bump version to <New Version>`.
    -   Push the changes to the remote repository.

4.  **Create GitHub Release**:
    -   Use the `gh` CLI to create a new release.
    -   Command: `gh release create <New Version> --title "<New Version>" --notes "<Changelog Content>"`
    -   Ensure the notes correspond exactly to the added changelog section.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/knuckleswtf) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
