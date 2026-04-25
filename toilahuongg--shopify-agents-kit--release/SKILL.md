---
name: release
description: Orchestrates the release process - Testing, Documentation, and Tagging. Use when preparing a new version release. Use when this capability is needed.
metadata:
  author: toilahuongg
---

# Release

Orchestrate the complete release process.

1. **Preparation**:
    * Identify the version number to release (from argument or `package.json`).

2. **Step 1: Verify (QA)**:
    * Load the `qa-specialist` agent.
    * Run available tests: `npm run test` (or equivalent).
    * **STOP** if tests fail.

3. **Step 2: Document (Writer)**:
    * Load the `technical-writer` agent.
    * Check `CHANGELOG.md`.
    * Add an entry for the new version if missing, summarizing recent commits.

4. **Step 3: Tag (Git)**:
    * Commit changes: `git add . && git commit -m "chore(release): v<VERSION>"`
    * Create tag: `git tag v<VERSION>`

5. **Completion**:
    * Notify the user that the release v<VERSION> is ready to be pushed.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/toilahuongg) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-12 -->
