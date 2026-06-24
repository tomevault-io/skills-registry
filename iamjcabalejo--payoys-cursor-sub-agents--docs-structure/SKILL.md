---
name: docs-structure
description: Structure technical documentation for APIs, guides, and README. Use when writing docs, README, API references, or working with technical-writer. Use when this capability is needed.
metadata:
  author: iamjcabalejo
---

# Docs Structure

## README Template
```
# Project Name
Brief description (1-2 sentences)

## Quick Start
- Prerequisites
- Install steps
- Run command

## Key Features
- Bullet list

## Configuration
- Env vars, config options

## Usage
- Examples

## Contributing / License
```

## API Reference
- Endpoint: method, path, description
- Parameters: name, type, required, description
- Response: status, body shape, example
- Errors: possible codes and meanings

## User Guide
- Task-oriented headings
- Step-by-step with numbered lists
- Code examples where relevant
- Screenshots for UI flows

## Principles
- **Audience first**: Match skill level and goals
- **Scannable**: Headings, bullets, short paragraphs
- **Examples**: Working code over abstract description
- **Up to date**: Remove outdated docs when changing behavior

---
> Source: [iamjcabalejo/payoys-cursor-sub-agents](https://github.com/iamjcabalejo/payoys-cursor-sub-agents) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-04 -->
