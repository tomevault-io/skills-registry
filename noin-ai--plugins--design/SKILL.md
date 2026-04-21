---
name: design
description: Include dark mode variant Use when this capability is needed.
metadata:
  author: noin-ai
---

# UI Design Skill

Design UI components and layouts using `noin-ai:designer` agent.

## Usage

```bash
/design <component>              # Design only
/design <component> --implement  # Design + code
/design <component> --dark       # Include dark mode
```

## Workflow

```
/design <component>
    ↓
Task(noin-ai:designer, component, options)
    ↓
Return design spec
    ↓
If --implement: Task(noin-ai:coder, implement design)
```

## Output

- Visual specification
- Component structure
- Accessibility considerations
- Responsive breakpoints
- Optional: Implementation code

## Examples

```bash
/design user profile card
/design navigation header --dark
/design login form --implement
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/noin-ai) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
