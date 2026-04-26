---
name: deliverable-selfloop
description: | Use when this capability is needed.
metadata:
  author: willoscar
---

# Deliverable Self-Loop (fix-on-fail)

Goal: converge a pipeline deliverable to a stable, reader-facing quality bar.

For the ideation path, the target is now a **brainstorm memo bundle**:
- `output/REPORT.md`
- `output/APPENDIX.md`
- `output/REPORT.json`
- plus the supporting trace chain under `output/trace/`

## Load Order

1. Always read `references/overview.md` first — defines trace chain, convergence model, and upstream routing.
2. Read `references/quality_checklist.md` before running the gate — lists required sections, quality tokens, and templated-language anti-patterns checked by the script.

## Inputs

- `output/trace/IDEA_SIGNAL_TABLE.md`
- `output/trace/IDEA_DIRECTION_POOL.md`
- `output/trace/IDEA_SCREENING_TABLE.md`
- `output/trace/IDEA_SHORTLIST.md`
- `output/REPORT.md`
- `output/APPENDIX.md`
- `output/REPORT.json`

## Output

- `output/DELIVERABLE_SELFLOOP_TODO.md` (report-class; always written)

## Workflow

1) **Run the gate** — run the script (see Script below) on the workspace. It checks trace presence, shortlist size, required sections, appendix quality, and templated language.

2) **Read the TODO report** — open `output/DELIVERABLE_SELFLOOP_TODO.md`. If `- Status: PASS`, the deliverable is done.

3) **Fix blockers** — for each issue listed under `## Remaining blockers`:
   - Missing `output/trace/IDEA_SIGNAL_TABLE.md` → run `idea-signal-mapper`.
   - Missing `output/trace/IDEA_DIRECTION_POOL.md` → run `idea-direction-generator`.
   - Missing `output/trace/IDEA_SCREENING_TABLE.md` → run `idea-screener`.
   - Missing `output/trace/IDEA_SHORTLIST.md` → run `idea-shortlist-curator`.
   - Missing REPORT sections → expand the report via `idea-memo-writer`.
   - Thin APPENDIX → enrich with per-paper reading guides.
   - Templated language → rewrite canned phrases to context-specific language.

4) **Rerun until PASS**.

## Acceptance

- `output/DELIVERABLE_SELFLOOP_TODO.md` exists and contains `- Status: PASS`.
- All 7 trace/output artifacts present and non-empty.
- Shortlist size is 3–5.
- No templated language detected.

## Script (optional; deterministic gate)

### Quick Start

- `python .codex/skills/deliverable-selfloop/scripts/run.py --workspace workspaces/<ws>`

### All Options

- `--workspace <dir>` (required)
- `--unit-id <U###>`
- `--inputs <semicolon-separated>`
- `--outputs <semicolon-separated>`
- `--checkpoint <C#>`

### Examples

- Run the gate on a brainstorm workspace:
  - `python .codex/skills/deliverable-selfloop/scripts/run.py --workspace workspaces/brainstorm-llm-agents`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/willoscar) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
