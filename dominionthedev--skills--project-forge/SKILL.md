---
name: project-forge
description: > Use when this capability is needed.
metadata:
  author: dominionthedev
---

# Project Forge — Instant Project Scaffolding

You scaffold production-ready projects in seconds. Before writing a single line of
code, you run the scaffold script to lay the entire foundation, then build on top of it.

---

## Workflow

### Step 1 — Run the scaffold script

```bash
python skills/project-forge/scripts/scaffold.py <type> <project-name> --output <dir>
```

**Types:**
| Type | What you get |
|---|---|
| `nextjs` | Next.js 14 App Router + Tailwind + Framer Motion + TypeScript strict |
| `astro` | Astro 4 + Tailwind + View Transitions |
| `go-service` | Go + Chi router + structured logging + graceful shutdown + Makefile |
| `go-cli` | Go + Cobra + Viper config + Makefile |
| `rust-cli` | Rust + Clap + Tokio + Anyhow + Tracing |
| `rust-service` | Rust + Axum + SQLx + JWT + Tokio |
| `flutter` | Flutter + Riverpod 2 + GoRouter + Material 3 | depreciated

```bash
# Examples
python scaffold.py nextjs my-saas --output ~/projects
python scaffold.py go-service payment-api --output ~/projects
python scaffold.py rust-cli file-watcher --output ~/projects
```

### Step 2 — Parse the output

The script outputs JSON. Extract:
- `path` — where files were created
- `files` — list of all generated files
- `next_steps` — commands to run immediately

### Step 3 — Report to user and continue building

Tell the user exactly what was scaffolded, then immediately continue with the
actual feature work they requested. Don't stop at the scaffold — that's just
the launch pad.

---

## What's included in each scaffold

### `nextjs`
- `package.json` with all prod + dev deps pinned
- `tsconfig.json` (strict mode)
- `tailwind.config.ts` with full CSS variable theme system
- `src/app/layout.tsx` with `next/font` and metadata
- `src/app/page.tsx` with Framer Motion entrance animation
- `src/app/globals.css` with light + dark CSS variables
- `src/lib/utils.ts` with `cn()` helper
- `.gitignore`, `.env.local`, `README.md`

### `go-service`
- `cmd/server/main.go` with graceful shutdown
- `internal/server/server.go` with Chi + CORS + middleware stack
- `internal/server/respond.go` with JSON helpers
- `Makefile` with dev/build/test/lint targets
- `.gitignore`, `README.md`

### `rust-cli`
- `Cargo.toml` with clap, tokio, anyhow, tracing, serde
- `src/main.rs` with derive-based CLI, subcommands, verbose flag
- Release profile optimized (LTO, strip)
- `.gitignore`, `README.md`

---

## Post-scaffold checklist

After scaffolding, always:
- [ ] Tell the user which files were created
- [ ] Ask if you should continue
- [ ] Show the next-steps commands
- [ ] Implement the actual feature/app they asked for
- [ ] Do NOT ask them to run the next steps themselves if you can do it

---

## Important: Script location

The scaffold script is at:
```
skills/project-forge/scripts/scaffold.py
```

It requires only Python 3.10+ stdlib (no pip install needed).

NOTE: this skill is to be upgraded

---
> Source: [dominionthedev/skills](https://github.com/dominionthedev/skills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-15 -->
