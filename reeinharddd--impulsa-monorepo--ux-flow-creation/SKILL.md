---
name: ux-flow-creation
description: Generate user experience flow documentation following the UX-FLOW template. Use when designing UI flows, documenting user journeys, or when the user asks for UX documentation. Use when this capability is needed.
metadata:
  author: reeinharddd
---

# UX Flow Creation Skill

> **Purpose:** Generate comprehensive user experience documentation following the project template. Creates consistent UX docs that link to related API and feature documentation.

## Trigger

**When:** User requests UX documentation OR new feature needs UI design
**Context Needed:** Feature requirements, user personas, screen designs
**MCP Tools:** `mcp_payment-syste_search_full_text`, `read_file`

## Required Sections

```markdown
# [Feature] - UX Flow

## User Personas

- Primary: [persona]
- Secondary: [persona]

## Screens

1. [Screen Name]
   - Purpose: ...
   - Key elements: ...
   - Interactions: ...

## User Journeys

### Happy Path

1. User does X
2. System responds with Y
3. User sees Z

### Error Handling

- Scenario: ...
- Display: ...
- Recovery: ...
```

## Screen Documentation Format

```markdown
### Screen: [Name]

**Purpose:** [What this screen accomplishes]

**Entry Points:**

- From: [Previous screen]
- Trigger: [User action]

**Key Elements:**
| Element | Type | Behavior |
|:--------|:-----|:---------|
| [name] | button | [action] |

**Exit Points:**

- To: [Next screen]
- Condition: [What triggers navigation]
```

## Accessibility Checklist

- [ ] Keyboard navigation
- [ ] Screen reader support
- [ ] Color contrast (WCAG AA)
- [ ] Touch targets (44x44px min)
- [ ] Focus indicators

## Reference

- [06-UX-FLOW-TEMPLATE.md](/docs/templates/06-UX-FLOW-TEMPLATE.md)
- [VISUAL-IDENTITY.md](/docs/process/standards/VISUAL-IDENTITY.md)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/reeinharddd) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
