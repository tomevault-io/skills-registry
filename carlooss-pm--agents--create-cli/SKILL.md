---
name: create-cli
description: > Use when this capability is needed.
metadata:
  author: carlooss-pm
---

# Create CLI

Design CLI surface area (syntax + behavior), human-first, script-friendly.

## Do This First

- Read `skills/create-cli/references/cli-guidelines.md` if present.
- Upstream guidelines: https://clig.dev/
- Ask only the minimum clarifying questions to lock the interface.

## Clarify (fast)

- Command name + one-sentence purpose.
- Primary user: humans, scripts, or both.
- Input sources: args vs stdin; files vs URLs; secrets (never via flags).
- Output contract: human text, `--json`, `--plain`, exit codes.
- Interactivity: prompts allowed? need `--no-input`?
- Config model: flags/env/config-file; precedence.
- Platform/runtime constraints.

## Deliverables

Produce a compact spec:
- Command tree + USAGE synopsis.
- Args/flags table (types, defaults, required/optional, examples).
- Subcommand semantics.
- Output rules: stdout vs stderr; TTY detection; `--json`/`--plain`.
- Error + exit code map.
- Safety rules: `--dry-run`, confirmations, `--force`, `--no-input`.
- Config/env rules + precedence.
- 5-10 example invocations.

## Default Conventions

- `-h/--help` always shows help.
- `--version` prints version to stdout.
- Primary data to stdout; diagnostics/errors to stderr.
- `--json` for machine output; `--plain` for stable line-based text.
- Prompts only when stdin is a TTY; `--no-input` disables prompts.
- Destructive ops: interactive confirmation + `--force` for non-interactive.
- Respect `NO_COLOR`, `TERM=dumb`; provide `--no-color`.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/carlooss-pm) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
