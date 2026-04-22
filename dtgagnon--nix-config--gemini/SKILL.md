---
name: gemini
description: Query Gemini CLI for quick lookups, deep research, and approach comparison Use when this capability is needed.
metadata:
  author: dtgagnon
---

# /gemini — Gemini CLI Workflows

Delegate queries to Gemini for fast lookups, web-backed research, and trade-off analysis.

## Subcommands

### `/gemini [query]`

Quick query. Default mode when no subcommand is given.

Run the following command and present the response:

!`gemini "$ARGUMENTS" -y -o json 2>/dev/null | jq -r '.response'`

**Use for:**
- Quick factual lookups and explanations
- Syntax questions in unfamiliar languages
- Alternative implementation ideas
- Non-code research and brainstorming

### `/gemini research [topic]`

Deep research with web search capabilities. Ask Gemini to search for current documentation, latest API changes, best practices, or package availability.

Run: !`gemini "Research the following topic, use web search if needed: $ARGUMENTS" -y -o json 2>/dev/null | jq -r '.response'`

Present the findings to help inform our implementation strategy.

### `/gemini compare [options-to-compare]`

Compare multiple approaches or perspectives with trade-off analysis.

Run: !`gemini "Provide 3 different approaches or perspectives for: $ARGUMENTS. Compare their trade-offs." -y -o json 2>/dev/null | jq -r '.response'`

Use this to explore alternative solutions before committing to an approach.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/dtgagnon) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-12 -->
