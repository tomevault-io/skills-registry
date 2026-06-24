---
name: health-check
description: Periodic Codex repo health check orchestrator. Runs CI status, AGENTS.md staleness audit, documentation accuracy audit, and Obsidian Vault sync if present, pausing for approval between steps and producing one consolidated report. Global and project-agnostic. Trigger when the user says "health check", "health-check", "repo health", "check repo health", "repo maintenance", "run a health check", "audit the repo", "check everything", "periodic maintenance", or "check if everything is up to date". SKIP when the user only wants one specific audit. Use when this capability is needed.
metadata:
  author: ada-ggf25
---

# Repo Health Check

Run the maintenance sequence a Codex-supported repo needs: CI health, Codex instruction
docs, user-facing docs, and knowledge base sync.

Sequence:

`diagnose-ci` -> `audit-agent-docs` -> `audit-docs` -> `obsidian-vault-sync` if a vault
exists.

## Operating Principles

1. Orchestrate, do not reimplement delegated skills.
2. CI health comes first.
3. Pause after each step and let the user skip any step.
4. Accumulate one consolidated final report.
5. Do not apply fixes automatically; each delegated skill owns its own approval gate.

## Procedure

### 0. Orient

- Confirm the cwd is the intended repo root.
- Detect vault: check for `<repo>/.obsidian-vault/`.
- Show the planned sequence and let the user drop steps.

### 1. CI Health

- If GitHub Actions exists and `gh` is available, check recent run status.
- If failing, invoke `diagnose-ci` to fetch logs, classify, and propose fixes.
- If `gh` is unavailable, note the gap and continue.

### 2. Codex Instruction Docs

- Invoke `audit-agent-docs` to score `AGENTS.md` and `AGENTS.override.md` files for
  staleness and coverage gaps.
- Carry its findings into the consolidated report.

### 3. Project Documentation

- Invoke `audit-docs` for user-facing and developer-facing docs.
- Carry its findings into the consolidated report.

### 4. Obsidian Vault

- If a vault exists, invoke `obsidian-vault-sync` with full scope unless the user
  narrows it.
- If no vault exists, record "Skipped - no vault detected".

### 5. Consolidate

Return a table like:

```text
Area              | Status | Findings
CI                | Clean  | 0 failing jobs
AGENTS.md audit   | Issues | N stale, M missing, P orphaned
Docs audit        | Issues | N high, M medium, P low
Vault sync        | Clean  | 0 findings
```

Then list next actions that still need user approval. Offer to save the report to
`.codex/health-check-<date>.md` only after approval.

## Guardrails

- Never fabricate findings; use only delegated skill output and observed local state.
- Do not silently skip unavailable skills; say what is missing.
- Do not auto-apply fixes from delegated steps.
- Keep the final report concise and actionable.

---
> Source: [ada-ggf25/AI-Tools](https://github.com/ada-ggf25/AI-Tools) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
