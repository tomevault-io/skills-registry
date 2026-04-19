---
name: code-patterns
description: Discovers and documents coding patterns, conventions, and architecture in the current project. Use when Claude needs to understand the project's style before making changes. Use when this capability is needed.
metadata:
  author: mabry1985
---

# Code Pattern Discovery

Analyze the codebase to understand patterns and conventions before making changes.

## What to look for:

1. **File organization** — How are files structured? What naming conventions are used?
2. **Import patterns** — How are modules imported? Relative vs absolute paths? Barrel exports?
3. **Error handling** — Try/catch? Result types? Error boundaries? Custom error classes?
4. **Naming** — camelCase, snake_case, PascalCase? Prefixes? Suffixes? Conventions for handlers, services, utils?
5. **Testing patterns** — Where do tests live? What frameworks? Co-located or separate directory?
6. **API patterns** — REST? GraphQL? How are endpoints structured? Middleware patterns?
7. **State management** — How is state managed? Global stores? Context? Props drilling?
8. **Configuration** — Where do configs live? Env vars? Config files? Defaults?

## Output:

Summarize the patterns you find so Claude can follow them when making changes. Be specific — include examples from actual files with file:line references.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mabry1985) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
