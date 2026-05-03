---
name: update-modules-doc
description: Scan all source modules and regenerate MODULES.md with up-to-date exports and descriptions Use when this capability is needed.
metadata:
  author: dailydotdev
---

## What I do

Regenerate the `MODULES.md` file at the project root so it accurately reflects every module in the package.

## Steps

1. **Scan entry points** — Find all `.ts` files matching `src/*/*.ts` (excluding `src/types/`). Each file is a module entry point.
2. **Read each file** — For every entry point, identify all named exports and their types/signatures. Read JSDoc comments to extract descriptions.
3. **Regenerate `MODULES.md`** — Overwrite the file using the exact format below. Modules should be listed in alphabetical order by their import path.

## Output format

The file must follow this exact structure:

```markdown
# Modules

All modules are importable via subpath exports from `@dailydotdev/node-common`.

## <directory>/<filename>

**Import:** `@dailydotdev/node-common/<directory>/<filename>`

<Brief description of what this module provides, derived from JSDoc or file contents.>

| Export         | Type               | Description                          |
| -------------- | ------------------ | ------------------------------------ |
| `<exportName>` | `<type signature>` | <description from JSDoc or inferred> |
```

Repeat the `## <directory>/<filename>` section for each module.

## Rules

- The module heading uses the path relative to `src/` without the `.ts` extension (e.g., `utils/env`, `logger`).
- If the filename is `index`, omit it from the heading and import path (e.g., `src/logger/index.ts` becomes `logger`, not `logger/index`).
- The import path is `@dailydotdev/node-common/<directory>/<filename>` (e.g., `@dailydotdev/node-common/utils/env`, `@dailydotdev/node-common/logger`).
- If a module has behavioral notes (e.g., environment-dependent behavior), include a bullet list below the description and above the exports table.
- Only include files that have at least one export. Skip empty files or files with no exports.
- Do not include files under `src/types/` — those are ambient type declarations, not importable modules.
- Always sort modules alphabetically by import path.
- Always sort exports alphabetically within each module.

## When to use me

Use this skill whenever a new module is added, an existing module's exports change, or a module is removed. This keeps `MODULES.md` in sync with the source code.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/dailydotdev) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
