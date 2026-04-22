---
name: decide
description: Log a decision with context, alternatives considered, and rationale. Use for "/decide project: using websocket over polling" or "/decide project: chose React for frontend". Use when this capability is needed.
metadata:
  author: kv0906
---

# /decide — Decision Logging

Record decisions with context and alternatives.

## Context

Today's date: `!date +%Y-%m-%d`
Recent decisions: `!ls decisions/*/*.md 2>/dev/null | tail -10`

Reference template: @_templates/decision.md
Config: @_core/config.yaml
Processing logic: @_core/PROCESSING.md

## Input

User input: $ARGUMENTS

## Processing Steps

1. **Parse Input**
   - Extract project (before colon)
   - Extract decision statement
   - Look for alternatives: "over", "instead of", "vs", "considered"
   - Extract context if provided

2. **Create Decision Note**
   - Filename: `decisions/{project}/{date}-{slug}.md`
   - Apply template with:
     - Decision statement
     - Alternatives considered
     - Context/rationale
   - Add `## Links` section

3. **Auto-Link**
   - Search for related docs in `docs/{project}/`
   - Add backlinks to relevant docs

4. **Update References**
   - Add to project index if exists

5. **Append to Vault Log**
   - Append entry to `01-index/_vault-log.md` (see `.claude/rules/vault-log.md`)
   - Action: `decision`
   - Details: the decision statement in ~10 words

## Output

```
Created: decisions/{project}/{date}-{slug}.md
Decision: {decision}
Alternatives: {count} considered
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/kv0906) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
