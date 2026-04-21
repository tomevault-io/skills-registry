---
name: claude-md-writer
description: Escribe y mejora archivos CLAUDE.md siguiendo best practices de Anthropic. Este skill se activa cuando el usuario dice "crear CLAUDE.md", "mejorar CLAUDE.md", "actualizar CLAUDE.md", "revisar CLAUDE.md", "escribir instrucciones del proyecto", "create CLAUDE.md", "improve CLAUDE.md", "review CLAUDE.md", "write project instructions", "optimize docs for Claude", "auditar CLAUDE.md", "audit CLAUDE.md", "limpiar CLAUDE.md", "dead weight", o configura un nuevo repositorio. Use when this capability is needed.
metadata:
  author: testacode
---

# CLAUDE.md Writer

A skill for creating and optimizing CLAUDE.md files following official Anthropic best practices.

## Golden Rule

Para cada linea, pregunta: *"Eliminar esto causaria que Claude cometa errores?"*
Si no, eliminalo. CLAUDE.md inflados causan que Claude ignore instrucciones.

> "Think of CLAUDE.md as the 'unwritten knowledge' in your codebase"

## Quick Tools

- `/init` - Genera CLAUDE.md inicial basado en la estructura del proyecto
- `#` key - Agregar instrucciones dinamicamente durante la sesion

## @import Syntax

CLAUDE.md puede importar otros archivos:
```markdown
See @README.md for overview and @package.json for commands.
Git workflow: @docs/git-instructions.md
```

## Reference Documentation

For examples and validation checklist, see:
- `references/good-example.md` - Well-structured CLAUDE.md example
- `references/bad-example.md` - Common anti-patterns to avoid
- `references/checklist.md` - Quality validation checklist
- `references/dead-weight-audit.md` - Dead weight audit (5 criterios para recortar instrucciones)

## What to Include vs Exclude

| Include | Exclude |
|---------|---------|
| Bash commands Claude can't guess | Anything Claude can figure out by reading code |
| Code style rules that differ from defaults | Standard language conventions |
| Testing instructions and preferred test runners | Detailed API docs (link instead) |
| Repository etiquette (branch naming, PR) | Information that changes frequently |
| Architectural decisions specific to project | Long explanations or tutorials |
| Developer environment quirks (env vars) | File-by-file descriptions |
| Common gotchas or non-obvious behaviors | Self-evident practices |

## CLAUDE.md vs Skills

| CLAUDE.md | Skills |
|-----------|--------|
| Reglas amplias que aplican siempre | Conocimiento de dominio solo relevante a veces |
| Code style y workflow | Workflows especializados reutilizables |
| Convenciones del repo | Info que cambia por contexto |
| Se carga en cada sesion | Se cargan on-demand |

## Content Triage

Antes de agregar contenido a CLAUDE.md, evaluar:

| Si el contenido es... | Entonces... |
|-----------------------|-------------|
| Regla que aplica a TODAS las tareas | Agregar a CLAUDE.md |
| Domain knowledge especifico | Sugerir crear skill |
| Workflow que solo aplica a veces | Sugerir crear skill |
| Info que cambia frecuentemente | No agregar, linkear a docs |

Si el usuario quiere agregar info condicional ("Cuando trabajes con X...", "Para tareas de tipo Y..."), sugerir crear un skill separado.

## Usage Modes

| Invocacion | Comportamiento |
|------------|----------------|
| `/claude-md-writer` | Proceso completo: analizar proyecto, escribir/mejorar CLAUDE.md |
| `/claude-md-writer actualiza con la sesion` | Extraer info relevante de la sesion actual y agregar a CLAUDE.md |
| `/claude-md-writer agrega regla: [descripcion]` | Agregar una regla especifica con el formato correcto |
| `/claude-md-writer revisa y optimiza` | Auditar CLAUDE.md existente, eliminar redundancias, mejorar enfasis |
| `/claude-md-writer audita` | Dead weight audit: aplicar los 5 criterios contra el setup actual (ver `references/dead-weight-audit.md`) |
| `/claude-md-writer convierte a skill` | Identificar contenido que deberia ser skill y extraerlo |

## Process

### Step 1: Analyze the Project

Identify tech stack, project structure, and existing documentation.

### Step 2: Apply the Three Dimensions

| Dimension | Question to Answer |
|-----------|-------------------|
| **WHAT** | What is this project? Tech stack? Structure? |
| **WHY** | Why does it exist? What problem does it solve? |
| **HOW** | How do I work on it? Commands? Workflows? |

### Step 3: Write Following Best Practices

**Length**: Keep under 200 lines (ideal ~100 lines)

**Structure** (in order):
1. Repository Overview (1 paragraph)
2. Why This Exists (purpose, users)
3. Quick Start & Commands (bash with descriptions)
4. Architecture Overview (key patterns)
5. Key Development Rules (with emphasis)
6. References (links to detailed docs)

**Emphasis for Critical Rules**:
```markdown
- **CRITICAL**: [Never violate this]
- **IMPORTANT**: [Always do this]
```

**Progressive Disclosure**:
- Use file references instead of code snippets
- Link to external docs for detailed topics
- Keep CLAUDE.md focused on universal instructions

### Step 4: Verify with Checklist

Before finalizing, run through the checklist in `references/checklist.md`.

## Template

```markdown
# CLAUDE.md

This file provides guidance to Claude Code when working with this repository.

## Repository Overview

[One paragraph: what this project is, tech stack, main purpose]

## Why This Exists

[What problem it solves, who uses it, business context]

## Quick Start & Commands

\`\`\`bash
npm ci        # Install dependencies
npm start     # Start dev server
npm test      # Run tests
npm run build # Production build
\`\`\`

## Architecture Overview

[Key patterns, folder structure, main concepts - keep brief]

## Key Development Rules

- **CRITICAL**: [Most important rule that must never be violated]
- **IMPORTANT**: [Second most important rule]

## References

- `/docs/` - Detailed documentation
- `README.md` - Project readme
```

## Dead Weight Audit

Cuando el usuario pida auditar su setup, aplicar los 5 criterios de `references/dead-weight-audit.md` contra cada regla:

1. **¿Default?** — Claude ya lo hace sin que se lo digan
2. **¿Conflicto?** — Contradice otra regla en el setup
3. **¿Redundancia?** — Cubierto por otra regla o proceso
4. **¿Parche puntual?** — Agregado para arreglar un output malo específico
5. **¿Vago?** — Se interpreta distinto cada vez

**Output esperado:**
- Lista de cortes con razón en una línea cada uno
- Lista de conflictos encontrados entre archivos
- Versión limpia del CLAUDE.md sin dead weight

**Proceso post-audit:** eliminar → probar 3 tareas típicas → re-agregar solo lo que se rompió.

## Anti-Patterns to Avoid

| Anti-Pattern | Why It's Bad | Do This Instead |
|--------------|--------------|-----------------|
| Code snippets | Become stale, waste tokens | Use `file:line` references |
| Styling rules | LLM is slow for deterministic tasks | Configure linters |
| Kitchen sink | Dilutes instruction effectiveness | Keep < 200 lines |
| No emphasis | Critical rules same weight as minor | Use CRITICAL/IMPORTANT |
| Auto-generated | Misses project nuances | Manually craft |
| Domain knowledge | Inflates every session | Move to skills |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/testacode) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
