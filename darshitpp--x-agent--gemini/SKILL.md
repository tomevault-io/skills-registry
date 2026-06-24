---
name: gemini
description: Delegate tasks or get a second opinion from Gemini CLI. Use when this capability is needed.
metadata:
  author: darshitpp
---

# Cross-Agent Task Runner — Gemini CLI

## Procedures

**Step 1: Self-Call Detection**

1. Check for `GEMINI_CLI` environment variable.
2. If detected, stop and inform the user:
   "Cannot invoke Gemini from within Gemini. Use a different target CLI."

**Step 2: Execute Shared Procedure**

1. Read `references/shared-procedure.md` for the core workflow
   (mode inference, context gathering, prompt construction, result presentation).
2. Read `references/cli-gemini.md` for Gemini-specific flags, model selection
   heuristics, and version compatibility matrix.
3. Follow the shared procedure using the Gemini-specific details.

---
> Source: [darshitpp/x-agent](https://github.com/darshitpp/x-agent) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
