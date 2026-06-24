---
name: cursor-control
description: Control code editors (VS Code, Cursor, Windsurf) via their CLI and extension APIs. Open files, run tasks, manage extensions, and automate development workflows. Use when this capability is needed.
metadata:
  author: noobclaw
---

# Cursor / VS Code Control Skill

## When to Use This Skill

Use this skill when the user wants to:
- Open files or projects in their code editor
- Install or manage editor extensions
- Run editor tasks or commands
- Set up development environments
- Navigate code (go to definition, find references)

## Editor CLI Commands

### VS Code / Cursor / Windsurf

All three editors share the same CLI interface:

```bash
# Detect which editor is available
which code 2>/dev/null || which cursor 2>/dev/null || which windsurf 2>/dev/null

# Open a file
code /path/to/file.ts
cursor /path/to/file.ts

# Open a folder as project
code /path/to/project
cursor /path/to/project

# Open file at specific line
code --goto /path/to/file.ts:42

# Diff two files
code --diff file1.ts file2.ts

# Install extension
code --install-extension ms-python.python
cursor --install-extension ms-python.python

# List installed extensions
code --list-extensions

# Uninstall extension
code --uninstall-extension extension-id

# Open new window
code --new-window

# Open settings
code --open-settings
```

### Detecting the Active Editor

```bash
# Windows
tasklist | findstr /i "code.exe cursor.exe windsurf.exe"

# macOS/Linux
ps aux | grep -E "code|cursor|windsurf" | grep -v grep
```

### Recommended Extensions to Install

When setting up a development environment, suggest these based on the project:

**Web Development:**
- `dbaeumer.vscode-eslint` — ESLint
- `esbenp.prettier-vscode` — Prettier
- `bradlc.vscode-tailwindcss` — Tailwind CSS

**Python:**
- `ms-python.python` — Python
- `ms-python.vscode-pylance` — Pylance

**General:**
- `eamodio.gitlens` — GitLens
- `usernamehw.errorlens` — Error Lens
- `christian-kohler.path-intellisense` — Path Intellisense

## Workflow Pattern

1. Detect which editor the user has installed
2. Use the appropriate CLI command
3. Verify the action completed successfully
4. Provide next steps or suggestions

---
> Source: [noobclaw/client](https://github.com/noobclaw/client) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
