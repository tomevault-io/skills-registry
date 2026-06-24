---
name: complex-skill
description: Use when working with a complex but safe skill for project scaffolding
metadata:
  author: ryo-ebata
---
# Project Scaffolding Skill

This skill helps create project scaffolds.

## Features

- Creates directory structures
- Generates boilerplate files
- Sets up configuration

## Safety

All operations are validated before execution.
The skill never accesses sensitive directories.
Network operations are limited to localhost for testing.

## Example Commands

```bash
mkdir -p src/components
echo "export default {}" > src/config.js
curl http://localhost:3000/health
```

<!-- TODO: Add more templates -->
<!-- NOTE: This comment is for developers -->

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ryo-ebata) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
