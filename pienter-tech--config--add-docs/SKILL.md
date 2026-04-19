---
name: add-docs
description: Add or improve inline code documentation for a target file using the language-appropriate doc format. Use when the user asks to document functions, classes, modules, APIs, or improve maintainability docs. Use when this capability is needed.
metadata:
  author: pienter-tech
---

# Add Docs

Document code clearly without over-documenting trivial logic.

## Process

1. Read the target file and identify public or non-obvious units.
2. Choose the appropriate format:
- PHP: phpDoc
- JS/TS: JSDoc
- Python: docstrings
- Java/Kotlin: Javadoc/KDoc
- C#: XML docs
- Go: Go doc comments
- Rust: `///`
- C/C++: Doxygen
- Shell: structured usage comments

3. For each meaningful function/class/module, document:
- Purpose
- Parameters
- Return values
- Errors/exceptions
- Usage examples only when complexity justifies it

## Rules

- Keep docs concise and accurate to current behavior.
- Prefer practical details over generic prose.
- Avoid documenting obvious one-liners.
- Update docs if implementation and existing comments disagree.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/pienter-tech) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
