---
name: acceptance-criteria
description: Expert guidance on writing effective acceptance criteria for user stories, including formats (Given/When/Then and checklist), best practices, and quality standards Use when this capability is needed.
metadata:
  author: thoroc
---

## What I do

I help you write clear, testable acceptance criteria for user stories by:

- Providing expert guidance on acceptance criteria formats (Given/When/Then and checklist)
- Reviewing existing acceptance criteria and suggesting improvements
- Helping choose the right format based on feature complexity
- Ensuring criteria are clear, concise, testable, and result-oriented
- Identifying missing scenarios (edge cases, error handling, negative paths)
- Validating that criteria follow best practices and avoid common pitfalls

## When to use me

Use this skill when you need to:

- Write acceptance criteria for new user stories
- Review and improve existing acceptance criteria
- Choose between Given/When/Then and checklist formats
- Ensure criteria are testable and unambiguous
- Train team members on acceptance criteria best practices
- Validate that acceptance criteria cover all scenarios
- Convert vague requirements into specific, measurable criteria

## How I work

When you engage me, I will:

1. **Understand the Context**: Ask about the user story, feature complexity, and target audience
2. **Choose Format**: Recommend Given/When/Then for user interactions or checklist for system rules
3. **Draft Criteria**: Create clear, testable acceptance criteria following best practices
4. **Cover Scenarios**: Ensure positive flows, negative scenarios, and edge cases are included
5. **Review Quality**: Validate criteria are measurable, result-oriented, and avoid implementation details
6. **Iterate**: Refine based on your feedback and team requirements

I focus on helping you describe WHAT the system should do, not HOW it should do it, ensuring all stakeholders understand when a story is complete.

---

# Acceptance Criteria: Core Guidance

## Overview

**Acceptance criteria (AC)** are the conditions a software product must meet to be accepted by a user, customer, or other systems. They are unique for each user story and define the feature behavior from the end-user's perspective.

## Purpose of Acceptance Criteria

1. **Making Feature Scope More Detailed** - Define boundaries and precise functionality details
2. **Describing Negative Scenarios** - Define invalid inputs and unexpected behavior handling
3. **Setting Communication** - Synchronize visions between client and development team
4. **Streamlining Acceptance Testing** - Provide basis for testing with clear pass/fail scenarios
5. **Conducting Feature Evaluations** - Enable accurate estimation and task division

---

## Essential Qualities of Good Acceptance Criteria

### 1. **Clarity**

Straightforward and easy to understand for all team members.

**Example**:

- ❌ Bad: "The system should handle errors"
- ✅ Good: "When a user enters an invalid email format, the system displays 'Please enter a valid email address' below the email field"

### 2. **Conciseness** - Communicate necessary information without unnecessary detail

### 3. **Testability** - Each criterion must be verifiable with clear pass/fail scenarios

- ❌ Bad: "The page should load quickly"
- ✅ Good: "The page loads in under 2 seconds for 95% of requests"

### 4. **Result-Oriented** - Focus on results, emphasizing the end benefit or value

- ❌ Bad: "Use React hooks to manage state"
- ✅ Good: "User session state persists across page refreshes"

---

## Acceptance Criteria Formats

### 1. Scenario-Oriented Format (Given/When/Then)

**Structure:**

```gherkin
Scenario: [Name for the behavior]
Given [the beginning state]
When [specific action that the user makes]
Then [the outcome of the action]
And [used to continue any statement]
```

**When to Use:**

- User interactions with clear sequences
- Testing scenarios with specific inputs and outputs
- Features requiring precise step-by-step validation
- When automated testing (BDD tools) will be used

**Example:**

```gherkin
Scenario: Forgot password

Given: The user navigates to the login page
When: The user selects <forgot password> option
And: Enters a valid email to receive a link for password recovery
Then: The system sends the link to the entered email
```

### 2. Rule-Oriented Format (Checklist)

A simple bullet list describing system behavior rules.

**When to Use:**

- System-level functionality requiring different QA methods
- Multiple independent rules or requirements
- Lists of validation rules
- UI/UX requirements

**Example:**

```markdown
Basic search interface acceptance criteria:
- The search field is placed on the top bar
- Search starts once the user clicks "Search"
- The placeholder disappears once the user starts typing
- Search is performed if a user types in a city, hotel name, or street
- The user can't type more than 200 symbols
```

---

## Best Practices

### DO

✅ **Keep Criteria Achievable** - Define reasonable minimum chunks of functionality
✅ **Make AC Measurable** - Outline scope clearly for accurate estimation
✅ **Avoid Technical Details** - Write in plain English for all stakeholders
✅ **Reach Consensus** - Ensure team and stakeholders agree
✅ **Write Testable AC** - Allow testers to verify all requirements
✅ **Write in Active Voice, First Person** - Reflect actual user words
✅ **Use Simple, Concise Sentences** - Multiple simple sentences beat one complex sentence
✅ **Focus on Result, Not Process** - Describe what should happen, not how

### DON'T

❌ **Don't Make AC Too Narrow** - Leave room for developer implementation choices
❌ **Don't Make AC Too Broad** - Keep them specific enough for validation
❌ **Don't Use Negative Sentences** - Avoid "not" unless presenting unique requirements
❌ **Don't Include Implementation Details** - Focus on behavior, not technical solution
❌ **Don't Write Ambiguous Criteria** - Each criterion should have clear pass/fail scenarios

---

## Examples: Good vs. Bad Acceptance Criteria

### Search Functionality

**Bad**:

```
- Search should work
- Results should be relevant
- It should be fast
```

**Good**:

```
Given: User is on the search page
When: User enters "laptop" in the search field
And: Clicks "Search" button
Then: Results display within 2 seconds
And: Results contain products with "laptop" in title or description
And: Results are sorted by relevance
```

### Form Validation

**Bad**:

```
- Validate the form
- Show errors
- Don't submit if invalid
```

**Good**:

```
- Email field validates format (user@domain.com pattern)
- Error message "Please enter a valid email" displays below field for invalid format
- Submit button is disabled when any field has validation errors
- Validation occurs on field blur (user leaves field)
- Error messages clear when user corrects the input
```

---

## Acceptance Criteria and Testing

Each acceptance criterion must be:

### Independently Testable

Can be verified in isolation from other criteria.

### Clear Pass/Fail Scenarios

No ambiguity in validation.

### Automatable

Where possible, support automated testing.

### Specific

Include measurable outcomes (time, quantity, quality, format, behavior).

---

## Integration with MoSCoW Prioritization

**Conditions of Satisfaction** combine MoSCoW priorities with acceptance criteria:

1. **Must Have Conditions**: These become your core acceptance criteria
2. **Should Have Conditions**: Document as "should" requirements
3. **Could Have Conditions**: List as enhancements; mark as optional
4. **Won't Have Conditions**: Document what is explicitly out of scope

---

## Common Pitfalls and Solutions

### Pitfall 1: Vague Requirements

**Problem**: "Make the system user-friendly"
**Solution**: "The user should be able to complete checkout in 3 steps or fewer"

### Pitfall 2: Untestable Criteria

**Problem**: "The system should be fast"
**Solution**: "The system should load search results in under 2 seconds for 95% of queries"

### Pitfall 3: Mixing Technical and Business Requirements

**Problem**: "Use React hooks for state management"
**Solution**: "The application should maintain user session state across page refreshes"

### Pitfall 4: Too Many Details

**Problem**: "Button should be 120px wide, 40px tall, #0066CC background..."
**Solution**: "Submit button follows brand design guidelines"

### Pitfall 5: No Negative Scenarios

**Problem**: Only covering the happy path
**Solution**: Include error scenarios, edge cases, and invalid inputs

---

## Summary

Effective acceptance criteria:

### Characteristics

✅ Clear and unambiguous
✅ Testable with pass/fail scenarios
✅ Written in plain English
✅ Focused on outcomes, not implementation
✅ Measurable with specific metrics
✅ Cover positive and negative scenarios

### Benefits

- Prevent misunderstandings
- Enable accurate estimation
- Support effective testing
- Align stakeholder expectations
- Reduce rework
- Facilitate communication

### Remember
>
> Acceptance criteria describe WHAT the system should do, not HOW it should do it.

Good acceptance criteria save time, prevent confusion, and ensure everyone understands what "done" means.

---

## Additional Resources

For detailed guidance, examples, and templates, see:

- `examples/` - Comprehensive examples by feature type
- `templates/` - Ready-to-use templates for JIRA and documentation
- `REFERENCE.md` - Complete reference guide with all formats and patterns

## References

- Acceptance Criteria Best Practices: <https://www.altexsoft.com/blog/acceptance-criteria-purposes-formats-and-best-practices/>
- Behavior-Driven Development (BDD): <https://en.wikipedia.org/wiki/Behavior-driven_development>
- Gherkin Language Specification: <https://cucumber.io/docs/gherkin/>
- Agile Alliance: <https://www.agilealliance.org/glossary/acceptance-criteria/>

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/thoroc) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
