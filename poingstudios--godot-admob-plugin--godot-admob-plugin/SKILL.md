---
name: release-manager
description: Manage and execute AdMob plugin releases. Use when bumping versions, generating changelogs, or validating GitHub release assets to ensure consistency across Godot, Android, and iOS. Use when this capability is needed.
metadata:
  author: poingstudios
---

# 🚀 AdMob Release Manager Protocol

Ensure consistency across all platforms and version strings during a release.

## 📋 Pre-Release Checklist

1. **Update Plugin Version**:
   - Modify `version="x.y.z"` in `platforms/godot_editor/addons/admob/plugin.cfg`.
2. **Verify C# Constants**:
   - Check if any C# version constants need updating (usually matches `plugin.cfg`).
3. **Verify Build Matrix**:
   - Ensure the latest Godot versions are included in `.github/workflows/cd-build-and-release.yml`.
4. **Changelog Generation**:
   - Run `git log $(git describe --tags --abbrev=0)..HEAD --oneline` to gather changes.
   - Categorize into `feat`, `fix`, and `chore`.

## 🛠️ Release Execution

1. **Trigger Workflow**:
   - Go to GitHub Actions -> `Build and Release` -> `Run workflow`.
2. **Validate Assets**:
   - Once the workflow finishes, verify that the following assets exist:
     - `poing-godot-admob-vX.Y.Z.zip` (Godot Editor Plugin).
     - `poing-godot-admob-android-v*.zip` (Android Templates).
     - `poing-godot-admob-ios-v*.zip` (iOS Templates).

## 🚫 Critical Rules
- Never release without updating `plugin.cfg`.
- Ensure the branch is `master` before triggering the release workflow.

---
> Source: [poingstudios/godot-admob-plugin](https://github.com/poingstudios/godot-admob-plugin) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-07-04 -->
