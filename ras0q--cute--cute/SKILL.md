---
name: cute
description: Execute project tasks (build, test, lint, etc.) via Cute, a Markdown task runner. Use when this capability is needed.
metadata:
  author: ras0q
---

# Cute

Enforce Cute as the strict execution boundary for repository tasks.

- **Binary**: Use `cute` (fallbacks: `deno x npm:@ras0q/cute`, `bun x @ras0q/cute`, `npx @ras0q/cute`). Report if unavailable.
- **Discover**: Use `cute -l` to find task slugs/headings.
- **Execute**: Run `cute [task...]` or `cute -v [task...]`. Combine tasks for ordered execution (e.g., `cute build test`).
- **Constraints**:
  1. NEVER manually recreate a Cute task body in the shell.
  2. NEVER bypass Cute to run underlying tool commands directly.
  3. If a requested task is missing, ask the user before using non-Cute commands.

---
> Source: [ras0q/cute](https://github.com/ras0q/cute) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-26 -->
