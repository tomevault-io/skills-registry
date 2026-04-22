---
name: biome-configuration-usage
description: Complete guide for configuring and using Biome for linting and formatting in Arlis. Use when this capability is needed.
metadata:
  author: rylezhou
---

# Biome Configuration & Usage

Biome is a fast formatter and linter that replaces Prettier and ESLint. This skill provides guidelines for configuring Biome, managing ignores, and fixing common issues.

## 1. Configuration (`biome.json`)

The core configuration lives in `biome.json` at the project root.

### Standard Structure
```json
{
  "$schema": "https://biomejs.dev/schemas/1.9.4/schema.json",
  "vcs": {
    "enabled": true,
    "clientKind": "git",
    "useIgnoreFile": true
  },
  "files": {
    "ignoreUnknown": false,
    "ignore": [] 
  },
  "formatter": {
    "enabled": true,
    "indentStyle": "tab"
  },
  "linter": {
    "enabled": true,
    "rules": {
      "recommended": true
    }
  }
}
```

> **Note:** The `files.ignore` property in `biome.json` is sometimes flagged as invalid depending on the schema version. The robust way to ignore files is using `.biomeignore`.

## 2. Ignoring Files

### `.biomeignore` (Recommended)
Place a `.biomeignore` file in the project root. It works exactly like `.gitignore`.

```text
# .biomeignore
node_modules
.next
dist
build
scripts/
```

### `ignore` vs `includes`
- **`files.includes`**: Whitelist specific files to process.
- **`.biomeignore`**: Blacklist files to exclude.

## 3. Common CLI Commands

- **Check (Lint + Format Verification)**:
  ```bash
  biome check .    # Check all files
  biome check src/ # Check specific folder
  ```

- **Apply Fixes (Safe)**:
  ```bash
  biome check --write .
  ```

- **Apply All Fixes (Unsafe)**:
  Use this for organizing imports or suppression sorting, but review changes carefully.
  ```bash
  biome check --write --unsafe .
  ```

- **Format Only**:
  ```bash
  biome format --write .
  ```

## 4. VS Code Integration

Ensure the **Biome** extension is installed and set as the default formatter for supported languages.

```json
// .vscode/settings.json
{
  "[javascript]": {
    "editor.defaultFormatter": "biomejs.biome"
  },
  "[typescript]": {
    "editor.defaultFormatter": "biomejs.biome"
  },
  "[typescriptreact]": {
    "editor.defaultFormatter": "biomejs.biome"
  },
  "[json]": {
    "editor.defaultFormatter": "biomejs.biome"
  }
}
```

## 5. Troubleshooting

- **`Property ignore is not allowed`**:
  If you see this error in `biome.json`, remove the `files.ignore` array and use `.biomeignore` instead.

- **"Imports are unused" errors**:
  Biome is strict about unused imports. If an import is only used for types, use `import type`.
  ```ts
  import type { User } from "./types";
  ```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/rylezhou) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
