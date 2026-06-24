---
name: type-ignorefix
description: | Use when this capability is needed.
metadata:
  author: bendrucker
---

# Type Ignore Fixer

Fix type errors instead of ignoring them.

## Scope

$ARGUMENTS

If no scope provided, scan the entire codebase.

## Type Ignores Found

!`rg '@ts-ignore|@ts-expect-error|eslint-disable|biome-ignore|type:\s*ignore|noqa|pylint:\s*disable|//nolint|//lint:ignore|#\[allow\(' -g '*.{ts,tsx,js,jsx,py,go,rs,rb}' -l 2>/dev/null || echo "none found"`

## Process

1. For each file above, spawn a `type-ignore:fixer` agent with prompt: "Fix type ignores in <file_path>"
2. Run type checking to verify fixes
3. Report summary: files fixed, ignores resolved, issues remaining

Spawn agents in parallel using multiple Task tool calls in a single message.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/bendrucker) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-12 -->
