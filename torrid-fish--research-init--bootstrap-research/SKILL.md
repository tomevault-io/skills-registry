---
name: bootstrap-research
description: Sets up a research/experiments project's CLAUDE.md and docs/ memory structure in one shot — a stronger replacement for /init for research work. Use this skill whenever the user starts a new research project, experiment notebook, paper repo, or scientific investigation; asks to "init", "initialize", "bootstrap", or "set up" a research repo; mentions creating CLAUDE.md for a study/experiment/paper workflow; or has a fresh repo containing data/, notebooks, papers, or experiment scripts. Auto-detects research signals (jupyter notebooks, scientific Python libs, papers/ or data/ folders, etc.) and generates a research-specific CLAUDE.md with reproducibility-first conventions, plus docs/ folder including experiments.md, findings.md, literature.md, progress.md, decisions.md, overview.md, and todo.md. Trigger this even when the user just says "/init" or "set up this project" in any repo that smells like research.
metadata:
  author: torrid-fish
---

# Bootstrap Research Project

Creates a research-optimized memory structure: lean CLAUDE.md as stable index, plus docs/ folder built around the experiment lifecycle (hypothesis → run → record → distill).

## Core principle

Separate **stable** (CLAUDE.md, every session) from **volatile** (docs/, on demand via `@` imports). Within docs/, separate **raw experiment log** (experiments.md, append-only) from **distilled insights** (findings.md, periodically updated).

## When to run

When the user is bootstrapping a research repo. Triggers: "init this", "set up CLAUDE.md", "bootstrap this", "memory for this experiment", "/bootstrap-research", or any `/init`-flavored ask in a repo with research signals.

## Steps

### 1. Confirm target directory — DO NOT ASSUME cwd

Before doing anything else:

- Run `pwd` and `ls -la`. If the cwd looks like `$HOME`, a mono-repo root, or a workspace containing multiple project folders, **stop and ask** which directory should be bootstrapped. Don't assume.
- If `CLAUDE.md` already exists in the target: read it first, then ask whether to merge, replace, or skip.
- If `docs/` already exists: list contents. Only generate files that don't already exist there.

### 2. Detect research context

Run in parallel where possible:

- **Research signals** (any of these strongly suggest research):
  - `*.ipynb` files (jupyter notebooks)
  - `data/`, `datasets/`, `raw/`, `derived/` directories
  - `papers/`, `references/`, `lit/` directories
  - `experiments/`, `runs/`, `logs/` directories
  - Scientific Python in `requirements.txt` / `pyproject.toml`: numpy, pandas, scipy, scikit-learn, torch, tensorflow, jax, transformers, statsmodels, matplotlib, seaborn, jupyter
  - R: `*.Rmd`, `renv.lock`
  - Existing `.bib` file or LaTeX sources
- **Tech stack** beyond Python: also check Cargo.toml, package.json (rare for research), etc.
- **Repo info**: `git log --oneline -5`, `git remote -v` for origin.
- **Project name**: prefer (in order) `pyproject.toml` `[project] name` → git remote repo name → directory name.

If signals are weak (no notebooks, no scientific libs, no data folders) — this might be a coding project, not research. **Stop and ask the user** whether they actually want the research template or something else.

- **Codebase signals** (these decide whether to generate `architecture.md`):
  - `src/` directory exists, OR
  - `pyproject.toml` / `setup.py` present, OR
  - 5+ `.py` files outside notebooks, OR
  - User-defined modules being imported across files (not just notebook-only code)

  If **any** signal hits → generate `architecture.md`. If none → skip it. A pure notebook-driven exploration doesn't need architecture notes yet; if it grows, the user can re-run this skill or add the file manually.

### 3. Generate files from templates

Templates live in this skill's `templates/` directory.

Copy each to the project, filling placeholders:

- `templates/CLAUDE.md` → `<project>/CLAUDE.md`
- `templates/docs/overview.md` → `<project>/docs/overview.md`
- `templates/docs/progress.md` → `<project>/docs/progress.md`
- `templates/docs/experiments.md` → `<project>/docs/experiments.md`
- `templates/docs/findings.md` → `<project>/docs/findings.md`
- `templates/docs/literature.md` → `<project>/docs/literature.md`
- `templates/docs/decisions.md` → `<project>/docs/decisions.md`
- `templates/docs/todo.md` → `<project>/docs/todo.md`
- `templates/docs/architecture.md` → `<project>/docs/architecture.md` **— only if codebase signals hit (see step 2)**

Placeholders:

| Placeholder | Source |
|---|---|
| `{{PROJECT_NAME}}` | pyproject.toml name → git remote → directory name |
| `{{RESEARCH_QUESTION}}` | If README has a clear research question, condense it; else `TODO: state the one question this project answers` |
| `{{ONE_LINE_DESCRIPTION}}` | README first paragraph, condensed; else `TODO:` marker |
| `{{TECH_STACK}}` | bullets: language, key libs (numpy/torch/etc.), notebook env if present |
| `{{FILE_MAP}}` | top-level dir tree with one-line descriptions |
| `{{TODAY_DATE}}` | today in `YYYY-MM-DD` |
| `{{ARCHITECTURE_LINE}}` | If codebase signals hit: `- `@docs/architecture.md` — swap points, frozen interfaces, pipeline shape`. Else: empty string (delete the line entirely so there's no blank bullet). |
| `{{ARCHITECTURE_CONVENTIONS}}` | If codebase signals hit, insert this block (with leading blank line):<br>`**Architecture (research-grade)**:`<br>`- Optimize for ablation-readiness, not maintainability. New components go behind swap points (see @docs/architecture.md).`<br>`- Don't change frozen interfaces — version them (v1 → v2) and note the experiment ID where v2 starts.`<br>`- Refactoring that changes past run behavior goes on a branch, never main.`<br>Else: empty string. |

If a signal is missing, leave a `TODO:` marker. Do not fabricate research questions or methodology.

### 4. Report back concisely

Print:
- File tree of what was created
- Detected context: project name, research signals found, tech stack
- Any `TODO:` markers requiring user input
- One-line reminder: "experiments.md is the most important file — log every run there before/during/after, not after the fact."
- Final tip: "Run `/populate-research-docs` (or just say `populate the docs`) to fill TODOs from git log, README, notebooks, and `papers/`. Safe to re-run anytime."

Don't echo full file contents.

## Hard don'ts

- **Don't run if research signals are absent** — ask the user to confirm; might be a coding project that needs a different template.
- **Don't skip the experiments.md emphasis** — research projects without an experiment log become unrunnable within weeks.
- **Don't overwrite existing files** without explicit confirmation.
- **Don't fabricate** research questions, methodology, or hypotheses. `TODO:` is fine.
- **Don't bloat CLAUDE.md** past ~120 lines. Workflow detail goes in `@docs/`.
- **Don't echo this skill's contents** to the user — just do the work.

## Why these specific files (for reference, not user-facing)

- `experiments.md` — append-only log; one entry per run; hypothesis + params + seed + result + raw output path. The single most important research artifact.
- `findings.md` — distilled insights extracted from experiments.md. Updated periodically. "What do we now believe?"
- `literature.md` — papers read, reading queue, inspirations. Research without lit awareness duplicates prior work.
- `decisions.md` — methodology choices (metric, baseline, ablation scope) — NOT individual runs (those are experiments).
- `architecture.md` — **conditional**, only if codebase signals hit. Tracks swap points, frozen interfaces, and pipeline shape — not production-style architecture.
- `progress.md` — session baton. Read at start, append at end.
- `overview.md` — research question, why it matters, scope, methodology summary.
- `todo.md` — open tasks across all of the above.

---
> Source: [torrid-fish/research-init](https://github.com/torrid-fish/research-init) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-15 -->
