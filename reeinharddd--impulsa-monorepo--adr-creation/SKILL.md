---
name: adr-creation
description: Generate Architecture Decision Records following the project template and numbering convention. Use when documenting architecture decisions, technical choices, or when the user asks to create an ADR. Use when this capability is needed.
metadata:
  author: reeinharddd
---

# ADR Creation Skill

> **Purpose:** Generate Architecture Decision Records (ADRs) following the project template and numbering convention.

## Trigger

**When:** User requests architecture documentation OR major technical decision made
**Context Needed:** Decision context, options considered, final decision
**MCP Tools:** `mcp_payment-syste_search_full_text`, `read_file`

## ADR Numbering

Format: `NNN-SHORT-TITLE.md`

```
001-AUTH-STRATEGY.md
002-OFFLINE-STRATEGY.md
003-[NEXT-DECISION].md
```

## Required Sections

```markdown
# ADR-NNN: [Title]

## Status

[proposed | accepted | rejected | deprecated | superseded]

## Context

[What is the issue that we're seeing that motivates this decision?]

## Decision

[What is the change that we're proposing and/or doing?]

## Options Considered

### Option 1: [Name]

**Pros:** ...
**Cons:** ...

### Option 2: [Name]

**Pros:** ...
**Cons:** ...

## Rationale

[Why was this option chosen?]

## Consequences

[What becomes easier or more difficult?]

## Related ADRs

[Links to related decisions]
```

## Frontmatter Template

```yaml
---
document_type: "adr"
module: "architecture"
status: "accepted"
version: "1.0.0"
last_updated: "YYYY-MM-DD"
author: "@username"

keywords:
  - "adr"
  - "[topic]"
  - "[technology]"

related_docs:
  database_schema: ""
  api_design: ""

adr_metadata:
  adr_number: NNN
  decision_date: "YYYY-MM-DD"
  stakeholders: ["@Architect", "@Backend"]
---
```

## ADR Categories

| Category     | Keywords           | Example                   |
| :----------- | :----------------- | :------------------------ |
| Architecture | pattern, structure | Monolith vs Microservices |
| Technology   | framework, library | Angular vs React          |
| Data         | database, storage  | PostgreSQL vs MongoDB     |
| Security     | auth, encryption   | JWT vs Sessions           |
| Integration  | api, protocol      | REST vs GraphQL           |

## Workflow

1. **Get next number** - Check existing ADRs in `docs/technical/architecture/adr/`
2. **Load template** - Read 02-ADR-TEMPLATE.md
3. **Gather context** - Document the problem
4. **List options** - At least 2 alternatives
5. **Document decision** - Clear statement
6. **Explain rationale** - Why this choice?
7. **Create file** - `docs/technical/architecture/adr/NNN-TITLE.md`

## Reference

- [02-ADR-TEMPLATE.md](/docs/templates/02-ADR-TEMPLATE.md)
- [001-AUTH-STRATEGY.md](/docs/technical/architecture/adr/001-AUTH-STRATEGY.md)
- [002-OFFLINE-STRATEGY.md](/docs/technical/architecture/adr/002-OFFLINE-STRATEGY.md)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/reeinharddd) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
