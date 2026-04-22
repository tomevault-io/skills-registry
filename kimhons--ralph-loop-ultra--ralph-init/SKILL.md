---
name: ralph-init
description: Initialize a new Ralph Ultra project with smart template detection, PRD scaffolding, and skill deployment. Use when starting a new project or onboarding an existing codebase into Ralph Ultra's autonomous development workflow. Use when this capability is needed.
metadata:
  author: kimhons
---

## Ralph Ultra Project Initialization

Initialize a project for autonomous development with Ralph Ultra.

### What this does

1. **Detects project type** — Node.js, Python, Rust, Go, Flutter, or custom
2. **Creates `.ralph-ultra/` directory** with config, skills, sessions, baselines, and cache
3. **Generates CLAUDE.md** — Tailored project instructions based on detected framework
4. **Deploys 21 production skills** — All skills copied and version-tracked
5. **Creates config.json** — Security mode, tool preference, iteration limits
6. **Generates prd.json scaffold** — Ready for story definition

### Usage

```
/ralph-ultra:ralph-init <project-name> [--template nextjs|python|node|flutter|fix]
```

### Arguments

- `$ARGUMENTS[0]` — Project name (required)
- `--template` — Force a specific template instead of auto-detecting

### Templates Available

| Template | Detected By | Includes |
|----------|------------|----------|
| `nextjs` | next.config.* | App Router patterns, ISR, Server Components |
| `python` | pyproject.toml, requirements.txt | FastAPI/Django patterns, venv, pytest |
| `node` | package.json (no Next.js) | Express/NestJS patterns, Jest |
| `flutter` | pubspec.yaml | Dart patterns, widget testing |
| `fix` | Any existing project | Minimal CLAUDE.md + PRD for bug fixing |

### Post-Init Checklist

After initialization, the skill verifies:
- [ ] `.ralph-ultra/` directory created with correct structure
- [ ] `config.json` has valid security mode (default: standard)
- [ ] Skills deployed and version manifest created
- [ ] CLAUDE.md generated with project-specific instructions
- [ ] `prd.json` scaffold ready for story definition

### Example

To initialize a Next.js project:
```
/ralph-ultra:ralph-init my-saas-app --template nextjs
```

Execute: `!bash -c "source $RALPH_ULTRA_HOME/lib/core/init.sh && ru_init_project '$ARGUMENTS'"` if ralph-ultra CLI is installed, otherwise follow the manual steps above.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/kimhons) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
