---
name: design-review
description: Use when refining an architecture or design document based on new learnings, before a rewrite. Use when you have an existing doc and related docs to cross-check against. Use when you need systematic section-by-section validation of decisions.
metadata:
  author: alizain
---

# Design Review

## Overview

**Design review separates validation from rewriting.** Go section-by-section through a design doc, cross-check against new learnings, record changes with rationale, then rewrite in a fresh session.

**Core principle:** Changes document first, rewrite second. Never edit inline.

## When to Use

- Refining v1 → v2 of an architecture doc
- Incorporating implementation learnings into design
- Cross-checking design against related docs
- Validating past decisions after new context

**Don't use:** Writing from scratch (use brainstorming), minor edits, obvious changes.

## Process

### 1. Create Changes Document

```markdown
# [Design Name] v[N+1]: Changes
> After full review, create fresh v[N+1] incorporating all changes.
## Changes
```

### 2. Section-by-Section Review

For each section:
1. Summarize current state
2. Cross-check against new learnings
3. Ask clarifying questions
4. Record change or confirm unchanged

**Questions to ask:**
- Does this still hold given what we now know?
- Anything missing? Contradicts other sections?
- Right level of detail for this doc?

### 3. Change Format

```markdown
### [N]. [Short Description]
**Section:** [Section name]
**Change:** [What's changing]
**Rationale:** [Why]
---
```

### 4. Structural Principles

| Principle | Meaning |
|-----------|---------|
| Main body vs Appendix | "What we're doing" vs "paths we rejected" |
| No mixed content | Remove sections with undiscussed details mixed in |
| Feature sections own data | Each feature owns its endpoints/models, no central lists |
| No timelines | Architecture = what, not when |

### 5. Fresh Session Rewrite

**Do NOT rewrite in same session.** Start fresh with:
- Original design doc
- Changes document
- Related docs (reference)

## Red Flags

- "I'll just fix this inline" → Use changes document
- "This section looks fine" → Confirm aloud or mark unchanged
- "I'll remember why" → Write rationale now
- "Let me rewrite now" → Fresh session required
- "Auto-generated but reasonable" → Validate or remove

## Common Mistakes

| Mistake | Fix |
|---------|-----|
| Inline editing | Always use changes document |
| Skipping sections | Review every section |
| No rationale | Always include "why" |
| Same-session rewrite | Fresh session for rewrite |
| Keeping auto-generated details | Validate each or remove |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/alizain) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
