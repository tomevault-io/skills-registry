---
name: adr
description: > Use when this capability is needed.
metadata:
  author: kevcofett
---

# Architecture Decision Records (ADR)

## Commands

### /adr new <title>
Create a new ADR. Prompts for context, options considered, decision, and consequences.

### /adr list
List all existing ADRs with number, title, status, and date.

### /adr update <number>
Update an existing ADR's status (proposed, accepted, deprecated, superseded).

### /adr search <keyword>
Search ADRs by keyword across titles and content.

## ADR Template

Every ADR follows this structure and is saved to `docs/decisions/`:

```markdown
# ADR-{NUMBER}: {TITLE}

**Status:** {proposed | accepted | deprecated | superseded by ADR-XXX}
**Date:** {YYYY-MM-DD}
**Deciders:** {who was involved}

## Context
What is the issue motivating this decision or change?

## Options Considered
1. **Option A** — description, pros, cons
2. **Option B** — description, pros, cons
3. **Option C** — description, pros, cons

## Decision
What is the change that we're proposing and/or doing?

## Consequences

### Positive
- What becomes easier or possible as a result?

### Negative
- What becomes harder or is a tradeoff?

### Risks
- What could go wrong? What's the mitigation?

## Related
- Links to related ADRs, PRs, issues, or external docs
```

## Procedure

1. **Check existing ADRs** — Read `docs/decisions/` to find the next available number and avoid duplicates
2. **Gather context** — Ask the user for the decision context if not provided
3. **Draft the ADR** — Fill in all sections of the template
4. **Present for review** — Show the draft to the user before saving
5. **Save** — Write to `docs/decisions/ADR-{NUMBER}-{slug}.md`
6. **Update index** — Append entry to `docs/decisions/DECISIONS.md` index table

## Numbering
ADRs are numbered sequentially starting from 001. The filename uses a slugified title:
`ADR-001-use-dataverse-for-entity-storage.md`

## Status Lifecycle
```
proposed → accepted → [deprecated | superseded by ADR-XXX]
```

An ADR should never be deleted. If a decision is reversed, create a new ADR that supersedes it and update the old one's status.

## MCMAP-Specific Considerations
When documenting decisions for this project, always evaluate:
- Impact on Dataverse schema (entities, attributes, OptionSets)
- Impact on Copilot Studio agent instructions (CRLF limits, plain text compliance)
- Impact on solution packaging and import pipeline
- Impact on multi-agent orchestration (12 agents, flow routing)
- Deployment branch implications (personal/mastercard/corporate)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/kevcofett) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
