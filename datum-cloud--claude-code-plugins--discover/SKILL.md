---
name: discover
description: > Use when this capability is needed.
metadata:
  author: datum-cloud
---

# Discovery Command

Shortcut to start feature discovery without manually creating pipeline artifacts.

## Usage

```
/discover <feature-description>
```

## Arguments

Feature description: $ARGUMENTS

## Workflow

1. **Generate feature ID**
   - Scan `.claude/pipeline/requests/` for existing IDs
   - Generate next sequential ID: `feat-{NNN}`

2. **Create request artifact**
   - Write to `.claude/pipeline/requests/{id}-{slug}.md`
   - Include initial description from user input

3. **Initialize pipeline state**
   - Create `.claude/pipeline/state/{id}.json`
   - Set `current_stage: "request"`

4. **Invoke product-discovery agent**
   - Pass the request artifact path
   - Agent will conduct discovery and produce brief

## Request Template

```markdown
---
handoff:
  id: feat-{NNN}
  from: user
  to: product-discovery
  created: {timestamp}
  context_summary: "{user's feature description}"
  decisions_made: []
  open_questions:
    - "What problem does this solve?"
    - "Who are the target users?"
  assumptions: []
---

# Feature Request: {Feature Name}

**Requested by**: User
**Date**: {date}

## Initial Description

{User's feature description}

## Notes

This request is awaiting discovery. The product-discovery agent will:
- Clarify the problem being solved
- Identify target users
- Assess scope boundaries
- Evaluate platform capability requirements
- Produce a discovery brief
```

## Example

```
/discover VM snapshot management for compliance requirements
```

Creates:
- `.claude/pipeline/requests/feat-042-vm-snapshot-management.md`
- `.claude/pipeline/state/feat-042.json`

Then invokes product-discovery agent to begin discovery.

## Output

```
Created feature request: feat-042-vm-snapshot-management
Artifact: .claude/pipeline/requests/feat-042-vm-snapshot-management.md
Pipeline state: .claude/pipeline/state/feat-042.json

Starting discovery phase...
[Invokes product-discovery agent]
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/datum-cloud) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
