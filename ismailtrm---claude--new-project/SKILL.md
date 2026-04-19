---
name: new-project
description: Scaffold Claude Code configuration for a new project Use when this capability is needed.
metadata:
  author: ismailtrm
---

# New Project Setup

Scaffold Claude Code configuration for a project directory.

## Workflow

### Phase 1: Detect Project Type

Analyze the current directory:

```bash
ls package.json pyproject.toml Cargo.toml go.mod pom.xml composer.json 2>/dev/null
```

Read the manifest file to detect:
- Language/runtime (Node, Python, Rust, Go, Java, PHP)
- Framework (Next.js, Express, FastAPI, Django, etc.)
- Key dependencies (Prisma, Supabase, Stripe, etc.)
- Test runner (Jest, Vitest, pytest, etc.)
- Linter/formatter (ESLint, Prettier, Ruff, etc.)

### Phase 2: Create CLAUDE.md

Generate a project-root `CLAUDE.md` with:

- **Commands**: Actual build/test/dev/lint commands from package.json scripts or equivalent
- **Architecture**: Directory structure overview based on what exists
- **Key files**: Entry points, config files
- **Testing**: Test command and patterns
- **Environment**: Required env vars (from .env.example if it exists)

Keep it concise. Only document what's real, not aspirational.

### Phase 3: Select MCP Servers

Based on detected dependencies, recommend MCP servers from `~/.claude/mcp-configs/mcp-servers.json`. Create a project-level `.mcp.json` with relevant servers.

| Dependency | MCP Server |
|-----------|------------|
| Supabase | supabase (plugin covers this) |
| Prisma/PostgreSQL | database server |
| Cloudflare Workers | cloudflare-docs, cloudflare-workers-builds |
| Vercel | vercel |
| Railway | railway |
| ClickHouse | clickhouse |

Only include servers the project actually needs. If plugins already cover it (github, supabase, context7), skip the MCP server entry.

### Phase 4: Project-specific Hooks

If the project has specific tooling, suggest additions to the project's `.claude/settings.json`:
- Formatter configured? Add PostToolUse auto-format hook
- Linter configured? Add PostToolUse auto-lint hook
- Test runner? Suggest test-on-edit hook

Ask before creating project-level hooks.

## Rules

- Never create files without reading the project first
- Only include real commands from the actual manifest
- Ask the user before creating .mcp.json or project hooks
- Keep CLAUDE.md under 60 lines

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ismailtrm) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
