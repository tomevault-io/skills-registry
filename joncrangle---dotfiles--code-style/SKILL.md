---
name: code-style
description: Discovery, typing, and execution standards. Use when this capability is needed.
metadata:
  author: joncrangle
---

<skill_doc>
# Code Style & Discovery

## 🔍 Discovery Phase (Mandatory)
Before writing any code:
1.  **Search**: Search for existing patterns using the `search_files` tool.
2.  **Read**: Read similar files to match style.
3.  **Types**: Find the TypeScript interfaces/types defined in the project.

## 🛡️ Coding Standards
- **Strict TypeScript**: No `any`. Define interfaces.
- **Error Handling**: Use `try/catch` with specific error logging. No silent failures.
- **Comments**: Comment *why*, not *what*.
- **Imports**: Use absolute imports if project configured (check `tsconfig`).

## 🧪 Verification
- **Test-Driven**: Create/Update tests for every logic change.
- **Lint**: Run linting before reporting success.

## 🛠️ Tooling
- Use `bun tools/hotspots.ts` to find frequently changed files.
- Use `list_files` to explore the directory structure.
</skill_doc>

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/joncrangle) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
