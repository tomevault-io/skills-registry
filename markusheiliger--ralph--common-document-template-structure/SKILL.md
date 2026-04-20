---
name: common-document-template-structure
description: Guide for maintaining consistent structure in Story and ADR document templates. Use this when creating or modifying document templates. Use when this capability is needed.
metadata:
  author: markusheiliger
---

## Common Document Template Structure

Story and ADR templates MUST follow a common base structure with type-specific extensions.

**Common Base Structure:**
1. YAML frontmatter with `state` and `score` fields
2. Document type heading (`# User Story` or `# Architectural Design Record (ADR)`)
3. `## Title` section
4. `## Status` section
5. `## Context` section
6. Type-specific sections

**DO:**
- User Stories: Include Acceptance Criteria (Gherkin Given/When/Then), Definition of Done, optional Notes
- ADRs: Include Decision, Alternatives Considered, Consequences (Positive/Negative/Neutral)
- Follow template precedence when best practices conflict with common structure

**DON'T:**
- Omit Status or Context sections from either template type
- Use non-standard section ordering
- Add duplicate sections from best practices that conflict with common structure

**Rationale:** Consistent structure reduces cognitive overhead and enables reliable CLI processing.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/markusheiliger) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
