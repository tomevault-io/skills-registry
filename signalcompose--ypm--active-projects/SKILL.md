---
name: active-projects
description: | Use when this capability is needed.
metadata:
  author: signalcompose
---

<!-- Language Handling: Check ~/.ypm/config.yml for settings.language -->
<!-- If language is not "en", translate all output to that language -->

# Active Projects

Display only active projects updated within 1 week from `~/.ypm/PROJECT_STATUS.md`.

## Prerequisites

- Run `/ypm:setup` first if `~/.ypm/config.yml` doesn't exist
- Run `/ypm:project-status-update` first if `~/.ypm/PROJECT_STATUS.md` doesn't exist

## Display Content

- Project name
- Overview
- Current branch
- Last update date
- Phase
- Implementation progress
- Next task

## Display Format

Show active projects in descending order by update date (newest first).

## Additional Information

- Total count of active projects
- Most progressed project
- Most recently updated project

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/signalcompose) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
