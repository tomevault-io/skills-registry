---
name: verification-gates
description: Enforces build, test, and lint gates with a self-correction loop and retry budget. Use when this capability is needed.
metadata:
  author: icampana
---

# Verification Gates & Self-Correction Loop

To ensure repository stability, all major changes must pass through a series of "Verification Gates."

## The Gates

1.  **Build Gate**: `npm run build` (or equivalent).
2.  **Test Gate**: `npm test` (or equivalent).
3.  **Lint Gate**: `npm run lint` or `ruff check`.

## The Self-Correction Workflow

Whenever a gate fails (e.g., via the `gate` tool), follow this loop:

1.  **Capture**: Inspect the `stderr` and `stdout` provided by the `gate` tool.
2.  **Analyze**: Explicitly state the root cause of the failure.
3.  **Fix**: Apply a patch or modification to address the cause.
4.  **Retry**: Re-run the `gate` tool with the same command.

## Retry Budget

- You have a **Retry Budget of 5 attempts**.
- If the gate still fails after 5 tries, **STOP IMMEDIATELY** and ask the human user for intervention. Do not continue trying variations.

## Operational Guidance

Always run the `gate` tool for any verification step. If a gate fails, announce "SELF-CORRECTION LOOP START [Attempt X/5]".

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/icampana) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
