---
name: design-create
description: Create design documents for features or systems. Use when architecture planning, API design, or technical decisions are needed before implementation. Use when this capability is needed.
metadata:
  author: hscspring
---

# Create Design Document

Create comprehensive design documents that guide implementation.

## Workflow

1. **Analyze** - Understand the requirement
2. **Research** - Check existing patterns in `wiki/tech/`
3. **Design** - Create architecture
4. **Document** - Write design doc
5. **Review** - Get approval

## Template

```markdown
# Design: [Feature Name]

## Overview
[What this covers]

## Goals
- Goal 1
- Goal 2

## Non-Goals
- What this does NOT cover

## Architecture

### Components
| Component | Responsibility |
|-----------|----------------|
| A | Does X |
| B | Does Y |

### API Design (if applicable)
#### POST /api/example
- Request: `{ "field": "value" }`
- Response: `{ "result": "value" }`

## Alternatives Considered
| Option | Pros | Cons |
|--------|------|------|
| A | ... | ... |

## Risks
| Risk | Mitigation |
|------|------------|
| Risk 1 | How to handle |
```

## Storage
Save to `wiki/design/[feature-name].md`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/hscspring) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
