---
name: user-story-writer
description: Use when converting requirements, ideas, or feature requests into user stories. Use after problem validated or requirement gathered. Produces properly formatted user stories with acceptance criteria, edge cases, and estimation guidance.
metadata:
  author: ethical-ai-syndicate
---

# User Story Writer

## Overview

Convert requirements, ideas, and feature requests into well-structured user stories that development teams can implement. Produces stories following the standard format with comprehensive acceptance criteria.

**Core principle:** A good user story is independent, negotiable, valuable, estimable, small, and testable (INVEST). If any element is missing, flag it.

## When to Use

- Converting stakeholder requests into backlog items
- Breaking down features into implementable stories
- Refining draft stories before sprint planning
- Standardizing story format across the team

## Story Format

```
As a [user type],
I want [goal/desire],
So that [benefit/value].
```

## Output Format

```yaml
user_story:
  id: "[Project]-[Number]"
  title: "[Brief descriptive title]"
  
  story_statement:
    as_a: "[User role/persona]"
    i_want: "[Action or capability]"
    so_that: "[Business value or outcome]"
  
  context:
    background: "[Why this story exists]"
    user_problem: "[Pain point being solved]"
    business_value: "[Quantified if possible]"
  
  acceptance_criteria:
    - given: "[Precondition]"
      when: "[Action taken]"
      then: "[Expected result]"
  
  edge_cases:
    - scenario: "[Edge case description]"
      expected_behavior: "[How system should handle]"
  
  out_of_scope:
    - "[Explicitly excluded functionality]"
  
  technical_notes:
    - "[Implementation hints or constraints]"
  
  dependencies:
    - "[Other stories or systems required]"
  
  estimation_guidance:
    complexity: "[Low | Medium | High]"
    uncertainty: "[Low | Medium | High]"
    suggested_points: "[Range, e.g., 3-5]"
  
  invest_check:
    independent: "[Yes/No - reasoning]"
    negotiable: "[Yes/No - reasoning]"
    valuable: "[Yes/No - reasoning]"
    estimable: "[Yes/No - reasoning]"
    small: "[Yes/No - reasoning]"
    testable: "[Yes/No - reasoning]"
```

## Story Quality Checklist

Before finalizing a story, verify:

### User Role Clarity
- [ ] User role is specific (not "user" or "someone")
- [ ] Role represents actual persona or job function
- [ ] Role has clear relationship to the system

### Goal Specificity
- [ ] Action is concrete and observable
- [ ] Goal is achievable within a sprint
- [ ] No compound goals (one story, one goal)

### Value Articulation
- [ ] Benefit is stated from user perspective
- [ ] Value is meaningful (not "so that I can use the feature")
- [ ] Business value can be measured or observed

### Acceptance Criteria
- [ ] Each criterion follows Given-When-Then format
- [ ] Criteria are testable (pass/fail determinable)
- [ ] Happy path covered
- [ ] Key error scenarios covered
- [ ] Edge cases identified

## Common Story Patterns

### CRUD Operations
```yaml
# Create
as_a: "Content manager"
i_want: "create a new blog post with title, body, and tags"
so_that: "I can publish content to engage our audience"

# Read
as_a: "Site visitor"
i_want: "view blog posts filtered by tag"
so_that: "I can find content relevant to my interests"

# Update
as_a: "Content manager"
i_want: "edit published blog posts"
so_that: "I can correct errors and keep content current"

# Delete
as_a: "Content manager"
i_want: "archive blog posts that are no longer relevant"
so_that: "visitors see only current content"
```

### Integration Stories
```yaml
as_a: "System administrator"
i_want: "the application to sync user data with our LDAP directory"
so_that: "users can authenticate with their corporate credentials"
```

### Performance Stories
```yaml
as_a: "Report user"
i_want: "the monthly summary report to load in under 3 seconds"
so_that: "I can quickly review performance during meetings"
```

## Anti-Patterns to Avoid

| Anti-Pattern | Problem | Fix |
|--------------|---------|-----|
| Technical story | "As a developer, I want to refactor..." | Frame as user value or create tech debt item |
| Compound story | "...and also..." | Split into multiple stories |
| Vague acceptance | "System works correctly" | Specific Given-When-Then |
| Missing value | "So that I can use it" | Articulate actual benefit |
| Too large | Spans multiple sprints | Decompose with epic-decomposition-planner |

## Story Sizing Guidance

| Size | Characteristics | Typical Points |
|------|-----------------|----------------|
| Small | Single screen, known pattern, no integration | 1-2 |
| Medium | Multiple states, some complexity, familiar domain | 3-5 |
| Large | Integration required, new patterns, uncertainty | 8-13 |
| Too Large | Multiple concerns, needs splitting | 13+ → Split |

## Red Flags in Your Output

If your story has these, revise:

- User role is "user" or "system"
- Goal contains "and" (compound)
- Value is circular ("so I can do the thing")
- No acceptance criteria
- Acceptance criteria aren't testable
- Dependencies not identified
- INVEST check has multiple "No" items

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ethical-ai-syndicate) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
