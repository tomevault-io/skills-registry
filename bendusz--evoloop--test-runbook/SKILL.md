---
name: test-runbook
description: Validate that commands in .plan/runbook.md are executable — checks executables, config files, environment variables, and TODO placeholders Use when this capability is needed.
metadata:
  author: bendusz
---

# Runbook Command Validator

## Step 1: Read Runbook

Read `.plan/runbook.md`. Missing → FAIL, stop. Empty → FAIL, stop.

## Step 2: TODO/TBD/FIXME Check

Case-insensitive scan. These block `validate_planning_exit_gate()`. Record line number, section, text. Flag as **blocking** at top of report.

## Step 3: Extract Commands

Parse fenced code blocks. For each: note section (Build/Test/Deploy/Rollback), extract command lines (skip comments/blanks), split multi-line (`&&`/`||`/`|`) into individual executables, note `$VAR`/`${VAR}` references.

## Step 4: Validate

**4a. Executable**: `command -v <base-executable>` (ignore sudo/env/nohup prefix). PASS/FAIL.

**4b. Config files**: Check implicit configs by tool:
`npm`→`package.json`, `make`→`Makefile`, `docker build`→`Dockerfile`, `docker-compose`→`compose.yml`, `cargo`→`Cargo.toml`, `go`→`go.mod`, `pip`→`requirements.txt`, `poetry`→`pyproject.toml`. Also check explicit paths in commands.

**4c. Env vars**: `printenv` for each `$VAR`. PASS (set) / WARN (not set, may be CI-injected).

**4d. Version** (optional): `<executable> --version`. PASS/WARN.

## Step 5: Report

```
Blocking: TODO/TBD/FIXME with line numbers
| Section | Command | Executable | Status | Notes |
Env vars: variable → set/not set
Fixes: concrete remediation per FAIL/WARN
Summary: "X commands, all passed" / "Z failures, Y warnings"
```

## Safety

NEVER execute deploy/rollback/destructive commands. NEVER run rm/delete/destroy/drop/kill. NEVER pipe to sh/bash/eval. Only: `which`, `command -v`, `--version`, `test -f`, `printenv`.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/bendusz) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
