---
name: unit-executor
description: | Use when this capability is needed.
metadata:
  author: willoscar
---

# Skill: unit-executor

## Goal

- Execute **one** unit end-to-end and leave the workspace in a consistent state.

## Inputs

- `UNITS.csv`
- Unit inputs listed in the row (files)

## Outputs

- Unit outputs listed in the row (files)
- Updated `UNITS.csv` status (`TODO → DOING → DONE/BLOCKED`)
- Optional: `STATUS.md` updated

## Procedure (MUST FOLLOW)

1. Load `UNITS.csv` and find the first unit with:
   - `status=TODO`
   - all `depends_on` units are `DONE`
2. Set its status to `DOING` and persist `UNITS.csv`.
3. Run the referenced skill (by following that skill’s `SKILL.md`).
4. Check the unit’s `acceptance` against produced artifacts.
5. If acceptance passes, set status to `DONE`; otherwise set to `BLOCKED` with a short note in `STATUS.md`.
6. Stop after one unit (do not start the next unit automatically).

## Acceptance criteria (MUST CHECK)

- [ ] Exactly one unit changes from `TODO` to `DONE/BLOCKED` (via `DOING`).
- [ ] Output files exist (or acceptance explicitly allows otherwise).

## Side effects

- Allowed: edit workspace artifacts (`UNITS.csv`, `STATUS.md`, unit outputs).
- Not allowed: modify `.codex/skills/` content.

## Script

### Quick Start

- `python .codex/skills/unit-executor/scripts/run.py --help`
- `python .codex/skills/unit-executor/scripts/run.py --workspace <workspace_dir>`

### All Options

- `--strict`: enable quality gate (blocks on scaffolds; writes `output/QUALITY_GATE.md`)

### Examples

- Run exactly one unit (strict):
  - `python .codex/skills/unit-executor/scripts/run.py --workspace <ws> --strict`
- Equivalent repo wrapper:
  - `python scripts/pipeline.py run-one --workspace <ws> --strict`

### Notes

- Returns 0 on `DONE/IDLE`, 2 on `BLOCKED/ERROR` (useful for automation).

## Troubleshooting

### Issue: no runnable unit is found

**Fix**:
- Check `UNITS.csv` for unmet dependencies, missing outputs, or `owner=HUMAN` units waiting on approvals in `DECISIONS.md`.

### Issue: a unit is marked `DONE` but outputs are missing

**Fix**:
- Fix the status to `TODO`/`BLOCKED` and re-run; only mark `DONE` when acceptance criteria and outputs are satisfied.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/willoscar) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
