---
name: check-unused-strings-ios
description: > Use when this capability is needed.
metadata:
  author: fgeistert
---

# Check Unused Strings

Find localization keys in `Strings.swift` that are never referenced in Swift source files.

## Step 1 — Run the script

```bash
python3 ~/.claude/skills/check-unused-strings-ios/scripts/checkUnusedStrings
```

The script always exits 0. Output is empty (with a success message) when all strings are
used, or lists unused `Strings.<name>` references when unused keys are found.

## Step 2 — Report findings

Present the results clearly to the user:

- If exit code is 0: confirm all strings are in use, no action needed.
- If exit code is 1: list the unused strings grouped by feature prefix (e.g. all `practice*`
  together, all `common*` together) so the user can see the scope at a glance.

**Do not remove anything yet.** Wait for the user to decide.

## Step 3 — Remove unused strings (only if asked)

If the user wants to remove unused strings:

1. For each unused key, remove its entry from the base `Localizable.strings` file (typically
   `en.lproj/Localizable.strings`) using the Edit tool.

2. If the project has a `buildStrings` script in the project root, run it to regenerate
   `Strings.swift` and any other derived string files:

   ```bash
   ./buildStrings
   ```

3. Verify by re-running the script to confirm no unused keys remain.

> **Note:** Removing a string key deletes it from the source file permanently. Only do
> this for keys you are certain are unused — check git history if in doubt.

---
> Source: [fgeistert/skills](https://github.com/fgeistert/skills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
