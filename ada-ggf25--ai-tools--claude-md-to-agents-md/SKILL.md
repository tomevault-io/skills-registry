---
name: claude-md-to-agents-md
description: Scans the current repo for CLAUDE.md files, finds directories that lack a matching AGENTS.md, translates each CLAUDE.md to an AGENTS.md draft (applying the Claude -> Codex substitution table), runs a $where-agents-md sanity check per candidate, and - with per-item approval - either writes the translated draft or invokes $init inline for a richer code-derived doc. Never overwrites an existing AGENTS.md. Global and project-agnostic. Trigger when the user says "claude md to agents md", "bootstrap agents md from claude md", "create agents md from claude md", "convert claude md to agents md", "port claude docs to codex", or "claude-md-to-agents-md". Use when this capability is needed.
metadata:
  author: ada-ggf25
---

# claude-md-to-agents-md

Global, project-agnostic skill. It finds every `CLAUDE.md` in the current repo,
identifies directories that have no matching `AGENTS.md`, translates each source file
using the Claude -> Codex substitution table, and - with per-item approval - writes the
draft or invokes `$init` inline for a richer code-derived alternative.

Use when: you've opened a Claude-first repo in Codex and there are zero or few
`AGENTS.md` files; the existing `CLAUDE.md` files already describe the directory's
purpose and conventions, so translation is a near-free win over starting from scratch.

## Procedure

### 1. Scan

```bash
find . -name CLAUDE.md \
  -not -path '*/node_modules/*' \
  -not -path '*/.git/*' \
  -not -path '*/vendor/*' \
  -not -path '*/.venv/*'
```

Also detect `AGENTS.override.md` files with a parallel find. Keep them as target-side
context - they are never translated or overwritten, but they may affect the approval
prompt for a directory that is missing shared `AGENTS.md` guidance.

### 2. Compute the gap

For each `<dir>/CLAUDE.md` found, check whether `<dir>/AGENTS.md` already exists.
Separate results into three buckets:

- **Gaps** - directory has `CLAUDE.md` but no `AGENTS.md` (action candidates)
- **Override-present gaps** - directory has `CLAUDE.md` and `AGENTS.override.md`, but
  no `AGENTS.md` (action candidates with an extra warning)
- **Already in sync** - both files exist (skip silently)

If there are no gaps or override-present gaps, report "All directories with CLAUDE.md
already have an AGENTS.md - nothing to do." and stop.

Present the gap list before doing anything else:

```text
Directories with CLAUDE.md but no AGENTS.md
  - src/backend/     (CLAUDE.md: 34 lines)
  - scripts/         (CLAUDE.md: 12 lines)

Already in sync (skipped)
  - .               (both files exist)

Needs extra confirmation - Codex override already present
  - .github/         (AGENTS.override.md exists - AGENTS.md would add shared guidance)
```

Ask the user which items to act on. **Do not proceed without explicit per-item approval.**

### 3. $where-agents-md sanity check (per approved candidate)

Before presenting the translated draft for a given directory, apply the
`$where-agents-md` scoring heuristic as a quick sanity check.

**Positive signals** (the directory likely warrants its own AGENTS.md):
- Has its own module manifest: `package.json`, `pyproject.toml`, `go.mod`, `Cargo.toml`,
  `setup.py`, `*.csproj`, `pom.xml`, etc.
- Uses a different language, framework, or build tool than the repo root.
- Has its own lint/format/test config or codegen fixtures.
- Contains >= 15 source files or is a clearly self-contained component.
- Sits at an architectural seam: `services/<x>`, `packages/<x>`, `apps/<x>`, `cmd/<x>`.

**Negative signals** (the directory may not need one):
- Tiny leaf dir with few files and no module boundary.
- Already fully described by an ancestor `AGENTS.md`.
- Contains only assets, generated files, or vendored/dependency code.

If the directory scores negatively, warn before showing the draft:
> "Warning: this directory may not warrant its own AGENTS.md - it has no module boundary
> and a parent instruction file may already cover it. Proceed anyway?"

If it scores positively, note the signal in the approval prompt (e.g., "This directory
has its own `package.json` - an AGENTS.md here is well-motivated.").

### 4. Translate and get approval

Read the `CLAUDE.md` and apply the substitution table in order:

| Find | Replace with |
|---|---|
| `CLAUDE.md` | `AGENTS.md` |
| `/<skill-name>` invocation syntax | `$<skill-name>` |
| `where-claude` cross-references | `where-agents-md` |
| `audit-claude-md` cross-references | `audit-agent-docs` |
| Claude config paths (`~/.claude/`, `.claude/`) | Codex config paths (`~/.codex/`, `.codex/`) |
| "Claude Code" when referring to the product | "Codex" |

Show the full translated draft to the user. For each item, offer two choices:

**Option 1 - Write this translated draft** as `<dir>/AGENTS.md`
Fast path; content mirrors the CLAUDE.md. Best when the source is high-quality and
detailed.

**Option 2 - Invoke `$init` inline** for this directory
Codex invokes `$init` targeted at `<dir>` in this same session - no navigation needed.
Produces a fresh, code-derived doc that may be richer than the translation. Best when
the CLAUDE.md is sparse or generic and the directory has substantial source code worth
deriving context from.

Require an explicit choice (1, 2, or skip) before moving to the next item.

### 5. Write

On **choice 1**: write `<dir>/AGENTS.md` with the translated content. Create
intermediate directories if needed.

On **choice 2**: invoke `$init` with `<dir>` as the target directory. `$init` will
analyze that directory's code and write its own `AGENTS.md`; record the outcome in the
wrap-up.

On **skip**: record the directory in the wrap-up; do not write anything.

### 6. Wrap-up

Report a summary in three sections:

```text
Created (translated draft)
  <dir>/AGENTS.md

Created (via $init)
  <dir>/AGENTS.md

Skipped
  <dir>  - already existed
  <dir>  - user declined
  <dir>  - negative $where-agents-md signal, user chose not to proceed
```

Note: AGENTS.md files are picked up automatically in the next Codex session opened in
that directory - no `./install.sh` step or restart required.

## Guardrails

- Never overwrite an existing `AGENTS.md`. Skip with a per-item warning if the target
  already exists.
- Never modify the source `CLAUDE.md`.
- Never write to global paths (`~/.claude/`, `~/.codex/`).
- Do not treat `AGENTS.override.md` as a replacement target; report it as existing local
  Codex override guidance and require explicit user approval before adding shared
  `AGENTS.md` guidance beside it.
- Require explicit per-item approval before every write.
- Apply the `$where-agents-md` sanity check and surface the result in the approval prompt;
  never silently create a file in a directory that clearly does not warrant one.
- When the CLAUDE.md is sparse or vague, surface this in the approval prompt and recommend option 2 (`$init`) over option 1.
- Create intermediate directories as needed, but only for approved writes.

---
> Source: [ada-ggf25/AI-Tools](https://github.com/ada-ggf25/AI-Tools) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-15 -->
