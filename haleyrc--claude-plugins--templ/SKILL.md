---
name: templ
description: General Templ development conventions and plugin information. Use when working in Go codebases containing Templ files (*.templ,*.templ.go) or needing Templ-specific guidance. Use when this capability is needed.
metadata:
  author: haleyrc
---

# Plugin Information

The `templ` plugin for Claude Code provides a number of utilities for working with Templ templates.

## Hooks

| Event         | Matcher      | Description                                                                                               |
|---------------|--------------|-----------------------------------------------------------------------------------------------------------|
| `PostToolUse` | `Edit|Write` | Formats all Templ files using `templ fmt` and generates the corresponding Go files with `templ generate`. |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/haleyrc) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
