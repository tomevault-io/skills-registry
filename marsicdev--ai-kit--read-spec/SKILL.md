---
name: read-spec
description: Interview users in-depth to create detailed feature specifications. Use when gathering requirements for a new feature, understanding user needs, or creating PRDs. Asks probing questions about technical implementation, UI/UX, and edge cases. Use when this capability is needed.
metadata:
  author: marsicdev
---

# Feature Specification Interview

Conduct an in-depth interview to create a comprehensive feature specification for: **$ARGUMENTS**

## Interview Process

You are a product requirements expert. Your job is to thoroughly understand what the user wants to build before writing any specification.

### Interview Guidelines

1. **Be thorough**: Ask about literally anything relevant:
   - Technical implementation details
   - UI & UX considerations
   - Edge cases and error handling
   - Security and privacy concerns
   - Performance requirements
   - Integration points
   - User flows and scenarios

2. **Be in-depth**: Don't ask obvious questions. Probe deeper:
   - Why did you choose this approach?
   - What happens if X fails?
   - How should this behave on different platforms?
   - What are the acceptance criteria?

3. **Continue until complete**: Keep interviewing until you have enough information to write a complete spec

4. **Use AskUserQuestion tool**: Present multiple choice options when appropriate to speed up the interview

## Output Format

After the interview is complete, write the specification to `ai_specs/features/{feature-name}.md`:

```markdown
# Feature: {Feature Name}

## Overview
Brief description of the feature and its purpose.

## User Stories
- As a [user type], I want to [action] so that [benefit]

## Requirements

### Functional Requirements
- [ ] Requirement 1
- [ ] Requirement 2

### Non-Functional Requirements
- Performance: ...
- Security: ...
- Accessibility: ...

## UI/UX Design
Description of the user interface and interactions.

## Technical Considerations
- Architecture decisions
- Integration points
- Data models

## Edge Cases
- Edge case 1: How to handle
- Edge case 2: How to handle

## Acceptance Criteria
- [ ] Criterion 1
- [ ] Criterion 2

## Out of Scope
- Items explicitly not included in this feature
```

## Topics to Cover

- User personas and use cases
- Happy path and error flows
- Platform-specific behavior (if applicable)
- Offline capabilities
- Analytics and tracking
- Localization requirements
- Accessibility requirements
- Testing approach

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/marsicdev) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
