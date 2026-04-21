---
name: req-create
description: Create structured requirement documents from user ideas. Use when capturing new features, tasks, or user stories that need documentation in requirement/INDEX.md. Use when this capability is needed.
metadata:
  author: hscspring
---

# Create Requirement

Create well-structured requirement documents from user ideas.

## Workflow

1. **Capture** - Get user's description
2. **Clarify** - Ask questions if needed
3. **Structure** - Format into template
4. **Index** - Add to `requirement/INDEX.md`
5. **Store** - Save to `requirement/in-progress/`

## Template

```markdown
# [REQ-XXX] Title

## Summary
[One sentence]

## User Story
As a [user], I want [goal], so that [benefit].

## Acceptance Criteria
- [ ] Criterion 1
- [ ] Criterion 2

## Priority
[P0: Critical | P1: High | P2: Medium | P3: Low]

## Effort
[S/M/L/XL]
```

## Tips
- Keep requirements atomic (one feature each)
- Make criteria testable
- Link to related designs

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/hscspring) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
