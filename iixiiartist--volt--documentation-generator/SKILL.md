---
name: documentation-generator
description: Generate project documentation from source code: API docs, README, architecture overview Use when this capability is needed.
metadata:
  author: iixiiartist
---
# Documentation Generator

Analyze source code to generate comprehensive documentation. Extracts module structure, public API surfaces, function signatures, and architecture patterns to produce README, API reference, and architecture documentation.

## Allowed Tools
- `read` - Read source files
- `grep` - Extract doc comments and signatures
- `glob` - Find all source files
- `write` - Write documentation files
- `bash` - Run documentation generators

## Documentation Outputs
1. README.md: Project overview, setup, usage, API
2. API Reference: Function/module signatures with descriptions
3. Architecture: Module hierarchy and data flow
4. Changelog: Recent changes from git log

---
> Source: [iixiiartist/volt](https://github.com/iixiiartist/volt) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
