---
name: docs
description: Auto-generate README and API documentation from code analysis Use when this capability is needed.
metadata:
  author: wonjangcloud9
---

# Documentation Generator

Generate comprehensive documentation by analyzing code.

## Process

1. **Analyze**: Scan codebase structure
2. **Extract**: Identify public APIs, types, configs
3. **Generate**: Create documentation
4. **Format**: Apply consistent Markdown styling

## Output Types

### README.md
- Project overview
- Installation
- Quick start
- API summary
- Configuration

### API Docs
- Module descriptions
- Function signatures
- Type definitions
- Usage examples

## Conventions

- Use JSDoc/TSDoc comments in source
- Markdown for all docs
- Code blocks with language hints
- Table of contents for long docs

## Example Usage

```bash
claudetree start --template docs "Generate documentation for the auth module"
claudetree start --skill docs "Document the API endpoints"
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/wonjangcloud9) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
