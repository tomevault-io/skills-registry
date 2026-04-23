---
name: gemini-deep-research
description: Run Gemini deep research to analyze and synthesize web sources into a comprehensive report. Invoked with a research query as argument. Use when this capability is needed.
metadata:
  author: uuta
---

# Gemini Deep Research

Use Google's Gemini `deep-research-pro-preview-12-2025` agent to perform deep web research and return a comprehensive markdown report with inline citations.

## Requirements

- `GEMINI_API_KEY` must be set in the environment
- `google-genai` Python package must be installed

## Procedure

1. Receive the user's research query from `$ARGUMENTS`
2. Ensure the `google-genai` package is available: run `pip install -q google-genai` via Bash
3. Run the research script via Bash in background mode (it may take several minutes):
   ```
   python skills/gemini-deep-research/scripts/gemini_deep_research.py "$ARGUMENTS"
   ```
4. Wait for the script to complete
5. Present the returned markdown report to the user as-is (it includes inline citations)
6. If the script fails, show the error output and suggest:
   - Check that `GEMINI_API_KEY` is set and valid
   - Check that the `google-genai` package is installed (`pip install google-genai`)
   - Check Google AI Studio status

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/uuta) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
