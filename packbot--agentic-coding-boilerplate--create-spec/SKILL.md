---
name: create-spec
description: Create a feature specification document. Use before implementing complex features to define requirements, API contracts, edge cases, and acceptance criteria. Use when this capability is needed.
metadata:
  author: packbot
---

# Create Spec: $ARGUMENTS

Create a feature specification for: **$ARGUMENTS**

Present the completed spec for review before implementation begins.

## Steps

1. **Understand the feature** — Ask clarifying questions if the requirements are ambiguous
2. **Research the codebase** — Find related modules, existing patterns, and potential integration points
3. **Write the spec** — Cover all sections below with concrete details
4. **Identify risks** — What could go wrong? What are the unknowns?
5. **Define acceptance criteria** — How do we know the feature is done?

## Spec Sections

### Summary
One paragraph: what the feature does, what problem it solves, who benefits.

### Detailed Design
- Expected behavior (inputs, outputs, side effects)
- API / interface contracts
- Data model changes (if any)

### Affected Modules
List every module/file that will be created or modified.

### Edge Cases
Null/empty inputs, large inputs, concurrent access, unavailable dependencies.

### Security & Performance
Input validation, auth requirements, query efficiency, caching needs.

### Acceptance Criteria
Specific, testable conditions that define "done".

## Quality Checklist

- [ ] Feature description is clear and unambiguous
- [ ] All affected modules/files are identified
- [ ] API contracts are defined (inputs, outputs, error cases)
- [ ] Edge cases are listed
- [ ] Acceptance criteria are testable
- [ ] Security implications are considered
- [ ] Performance implications are considered

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/packbot) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
