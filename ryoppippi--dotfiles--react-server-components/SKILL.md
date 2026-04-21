---
name: react-server-components
description: Review React code for proper Server/Client Component boundaries based on Next.js and React documentation. Identifies misplaced 'use client' directives and suggests optimal component splitting. Use when this capability is needed.
metadata:
  author: ryoppippi
---

<!--
Example prompts:
  /react-server-components
  /react-server-components scope=src/app/
  /react-server-components fix=false
-->

Arguments:

- scope: what to analyse (default: your current changes). Examples: "diff to main", "PR #123", "src/app/", "whole codebase"
- fix: whether to apply fixes (default: true). Set to false to only propose changes.

Steps:

1. Read https://react.dev/reference/rsc/server-components and https://nextjs.org/docs/app/building-your-application/rendering/composition-patterns to understand the guidelines
2. Analyse the specified scope for Server/Client Component anti-patterns
3. If fix=true, apply the fixes. If fix=false, propose the fixes without applying.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ryoppippi) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
