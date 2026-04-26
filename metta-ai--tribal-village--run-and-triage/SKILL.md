---
name: run-and-triage
description: Execute provided command(s), capture failures, and summarize root cause and fixes. Use when asked to run a command and report errors. Use when this capability is needed.
metadata:
  author: metta-ai
---

# Run and Triage

## Workflow
- Run the provided command(s) as-is; add a reasonable timeout if it is a recipe run.
- Capture stdout/stderr and any error output.
- Summarize failures and likely root cause; propose fixes.
- If a recipe run fails, point to relevant files/lines and suggest next steps.
- Re-run only if requested.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/metta-ai) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
