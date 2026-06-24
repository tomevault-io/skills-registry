---
name: sync-agents-docs
description: Sync AGENTS.md files, skills, and references after buildSrc or convention changes. Use after modifying build plugins, formatting rules, testing conventions, or task names to keep documentation consistent. Use when this capability is needed.
metadata:
  author: mongodb
---
# Sync Documentation After Build Changes

This is a manual checklist — `disable-model-invocation: true` means agents cannot execute it autonomously.
Human developers should walk through this after buildSrc changes.

After modifying `buildSrc` or build conventions, check whether AGENTS.md files, skills, and references need updating.
Review your changes against the checklist below and update only the affected files.

## Checklist

| What changed | What to update |
| --- | --- |
| Formatting conventions (`spotless.gradle.kts`) | Root `AGENTS.md` Style section, `style-reference` reference, affected module AGENTS.md files |
| Convention plugins added or removed | `buildSrc/AGENTS.md` plugin table, root `AGENTS.md` if build commands or workflow changed |
| Testing conventions (`testing-*.gradle.kts`) | Root `AGENTS.md` Testing section, `testing-guide` reference, affected module AGENTS.md files |
| Project plugins added or removed | `buildSrc/AGENTS.md` project plugin table |
| Build commands or task names changed | Root `AGENTS.md` Build and Before Submitting sections, `evergreen` skill, module AGENTS.md files |
| CI/CD or publishing changes | `evergreen` skill |

## How to use

1. Review the diff of your buildSrc changes
2. Walk through the checklist above — only rows matching your changes need action
3. Update the affected files, keeping changes minimal and accurate
4. Verify no stale references remain (e.g., renamed tasks, removed plugins)

---
> Source: [mongodb/mongo-java-driver](https://github.com/mongodb/mongo-java-driver) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-17 -->
