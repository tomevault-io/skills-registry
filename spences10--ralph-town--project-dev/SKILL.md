---
name: project-dev
description: Ralph-town development guidelines. Use for code style, project structure, and build commands. Use when this capability is needed.
metadata:
  author: spences10
---

# Ralph-Town Development

## Code Style

- **snake_case** - functions and variables
- **PascalCase** - classes
- **SCREAMING_SNAKE_CASE** - constants
- Prettier: tabs, single quotes, trailing commas, 70 char width
- Use Bun, not npm/pnpm

## Project Structure

```
packages/
├── cli/                 # Main CLI (ralph-town command)
│   └── src/
│       ├── sandbox/     # Sandbox module (create, manage)
│       └── commands/    # CLI commands
└── mcp-ralph-town/      # MCP server wrapping CLI
```

## Key Files

- `packages/cli/src/sandbox/` - Core sandbox module
- `packages/cli/src/commands/sandbox/` - CLI commands
- `docs/RESEARCH.md` - Architecture and findings

## Commands

```bash
bun dev          # Development mode
bun run build    # Compile TypeScript
```

## Don't

- Don't run lint:fix
- Don't guess Daytona APIs - check docs first

## Notes

- This is a monorepo with Bun workspaces
- CLI and MCP server share sandbox module code
- Check `docs/RESEARCH.md` for Daytona SDK findings

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/spences10) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
