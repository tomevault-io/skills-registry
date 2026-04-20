---
name: firefly-expressive-code-config
description: 修改 Firefly 代码高亮配置。Use when changing dark and light code themes or collapsible code behavior in src/config/expressiveCodeConfig.ts. Use when this capability is needed.
metadata:
  author: godblf
---

# Firefly Expressive Code Config

## Overview

Tune syntax highlighting theme pair and long-code collapse strategy.

## Workflow

1. Read the current target file(s) before editing.
2. Confirm the requested behavior and map it to existing keys.
3. Apply the smallest possible change inside the declared scope.
4. Keep existing style, object structure, and value types.
5. Report changed keys and paths.

## Edit Scope

- src/config/expressiveCodeConfig.ts

## Common Keys

- darkTheme
- lightTheme
- pluginCollapsible.enable / lineThreshold / previewLines / defaultCollapsed

## Guardrails

1. Do not modify files outside Edit Scope unless explicitly requested.
2. Keep booleans, numbers, arrays, enums, and URLs in valid types.
3. Preserve existing comments unless they conflict with the new behavior.
4. Prefer minimal diffs that are easy to review.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/godblf) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
