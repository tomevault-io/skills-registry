---
name: standards-list
description: List available GitLab CI standards topics. Use when this capability is needed.
metadata:
  author: onesmartguy
---

# GitLab CI Standards - List Topics

List the available standards topics that can be used with `/gitlab-ci:standards-view`, `/gitlab-ci:standards-load`, and `/gitlab-ci:standards-audit`.

## Available Topics

| Topic | Description |
|-------|-------------|
| `job-ordering` | Pipeline job ordering with `needs` vs `dependencies` |

## Usage

```
/gitlab-ci:standards-view [topic]
/gitlab-ci:standards-load [topic]
/gitlab-ci:standards-audit [topic]
```

If no topic is specified, all standards are included.

## Related Commands

- `/gitlab-ci:standards-view` — Display standards summary to the user
- `/gitlab-ci:standards-load` — Load full standards into context
- `/gitlab-ci:standards-audit` — Analyze repo for violations

## Instructions

When this skill is invoked, output the table of available topics above so the user knows what options they have.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/onesmartguy) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
