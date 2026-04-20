---
name: variable-name-update
description: Guidelines and systematic process for updating variable, function, and class names to improve code readability and maintainability. Use when this capability is needed.
metadata:
  author: kishan04rajput
---

# Variable Name Update Skill

Use this skill when you need to rename variables, functions, classes, or other identifiers. Clear naming is fundamental to code maintainability.

## 1. Naming Best Practices

- **Be Descriptive**: A variable name should clearly describe the data it holds (e.g., `userList` instead of `ul`).
- **Avoid Abbreviations**: Use full words unless the abbreviation is universally understood (e.g., `id`, `html`).
- **Use Consistent Casing**:
  - **JavaScript/TypeScript**: `camelCase` for variables and functions, `PascalCase` for classes.
  - **Python/Ruby on rails**: `snake_case` for variables and functions, `PascalCase` for classes.
- **Context Awareness**: Use names that make sense within their scope. In a short loop, `i` might be fine, but for a global constant, `MAX_RETRY_COUNT` is better.
- **Boolean Naming**: Use prefixes like `is`, `has`, or `can` (e.g., `isVisible`, `hasPermission`).

## 2. Systematic Update Process

### A. Identify and Scope
- **Target**: Clearly identify the identifier(s) to be renamed.
- **Scope**: Determine if the change is local (within a function), module-level, or global (used across multiple files).
- **Impact**: Check for usages in string literals, dynamic property access (e.g., `obj[dynamicKey]`), or external APIs.

### B. Execute Rename
- **LSP/Refactoring Tools**: If your IDE/environment supports it, use automated rename tools to ensure all references are updated reliably.
- **Manual Grep**: If automated tools are unavailable, use `grep` or `ripgrep` to find all occurrences across the project.
- **Minimum Change**: Only change the necessary identifiers to avoid unnecessary diff noise.

### C. Verify and Validate
- **Code Integrity**: Ensure that the logic remains unchanged. Renaming should be a "pure" refactor.
- **Build/Lint**: Run tests and linters to ensure no syntax errors were introduced.
- **Regression Check**: Verify that related functionalities still work as expected.

## 3. Common Pitfalls to Avoid

- **Shadowing**: Don't rename a variable to match one in an outer scope.
- **Magic Strings**: Be careful when renaming keys that are also used as strings (e.g., in JSON or database queries).
- **Breaking Public APIs**: If the identifier is exported or part of a public API, consider the impact on consumers.

> [!IMPORTANT]
> Always think before you rename. A good name should explain "what" it is and "why" it exists, not "how" it is used.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/kishan04rajput) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
