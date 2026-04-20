---
name: generic
description: Write, edit, and lint documentation files Use when this capability is needed.
metadata:
  author: ardaglobal
---

# Generic/Documentation Skill

You are working in a documentation and general-purpose environment.

## Available Tools

- `markdownlint` - Markdown linter
- `prettier` - File formatter (markdown, yaml, json)
- `pandoc` - Document converter
- `aspell` - Spell checker

## Project Detection

This runtime handles:
- Markdown files (`.md`, `.mdx`)
- JSON files (`.json`)
- YAML files (`.yaml`, `.yml`)
- Documentation directories (`docs/`, `documentation/`)

## Common Workflows

### Lint Markdown
```bash
markdownlint "**/*.md" 2>&1
```

### Format Files
```bash
# Markdown
prettier --write "**/*.md" 2>&1

# YAML
prettier --write "**/*.yaml" "**/*.yml" 2>&1

# JSON
prettier --write "**/*.json" 2>&1
```

### Check Formatting
```bash
prettier --check . 2>&1
```

### Spell Check
```bash
aspell check README.md
```

### Convert Documents
```bash
# Markdown to HTML
pandoc README.md -o README.html

# Markdown to PDF (requires LaTeX)
pandoc README.md -o README.pdf
```

## Markdown Best Practices

1. Use consistent heading levels
2. Add blank lines around code blocks
3. Use reference-style links for repeated URLs
4. Keep lines under 120 characters when possible

## File Types Handled

- `.md` - Markdown
- `.mdx` - MDX (Markdown + JSX)
- `.json` - JSON configuration
- `.yaml`/`.yml` - YAML configuration
- `.txt` - Plain text

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ardaglobal) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
