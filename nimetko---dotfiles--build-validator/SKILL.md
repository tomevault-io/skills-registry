---
name: build-validator
description: Validate local or CI builds, tests, and lint runs; interpret failures; report pass/fail and next steps. Use when asked to run or verify build/test/lint commands, confirm reproducibility, or triage CI logs. Use when this capability is needed.
metadata:
  author: nimetko
---

# Build Validation

## Workflow
1. Locate project tooling (README, Makefile, package.json, pyproject.toml, etc.).
2. Choose the minimal command set needed to validate (`build`, `test`, `lint`).
3. Run commands in a clean state; capture exact command and cwd.
4. Summarize results per command with pass/fail and key errors.
5. Propose minimal fixes or focused debugging steps.

## Output
- Commands run (in order)
- Result summary (pass/fail)
- Key errors (first relevant stack trace)
- Suggested fix or next action

## Guardrails
- Avoid destructive commands or network calls unless requested.
- Ask before running long or expensive builds.
- Do not change code unless explicitly asked; suggest patches instead.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/nimetko) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
