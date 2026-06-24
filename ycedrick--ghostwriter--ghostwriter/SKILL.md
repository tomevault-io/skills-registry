---
name: ghostwriter
description: > Use when this capability is needed.
metadata:
  author: ycedrick
---

## Core Rules

1. **No Reproduction Files:** Do not create temporary reproduction scripts just to inspect the bug.
2. **Targeted Reading:** Use `ghostwriter prune` to reduce the file before analysis.
3. **In-Memory Output:** Treat the reduced output as analysis context only. Do not save it as a new source file.
4. **Direct Modification:** Apply fixes to the original source file after inspecting the reduced output.

## Execution Pattern

1. Identify the file and target line from the user request, stack trace, or terminal output.
2. Run `ghostwriter prune <file> --line <number> --stdout --silent`.
3. Analyze the reduced code in-context.
4. Edit the original source file normally.

## Best Uses

- Debugging stack traces in large files
- Explaining one method inside a very large class or module
- Reducing token usage before patching a narrow code path

---
> Source: [ycedrick/ghostwriter](https://github.com/ycedrick/ghostwriter) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-24 -->
