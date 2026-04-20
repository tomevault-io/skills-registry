---
name: repo-hygiene-and-tooling
description: Use when cleaning up a repo: structure, scripts, docs, CI hooks, and developer ergonomics.
metadata:
  author: juantaco4you
---

## Goal
Make the repository easier to build, test, and contribute to.

## Workflow
1. Ensure a working README:
   - Setup
   - Build/run
   - Test
   - Common troubleshooting
2. Standardize scripts:
   - One command to test
   - One command to lint/format
   - One command to build
3. Add/verify tooling config:
   - Formatter
   - Linter
   - Type checker (if applicable)
4. CI sanity:
   - Ensure CI runs the same commands as local dev
5. Remove obvious foot-guns:
   - Hard-coded paths
   - Unused scripts
   - Stray debug logs

## Output format
- Changes made by category
- New/updated commands
- Notes for contributors

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/juantaco4you) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
