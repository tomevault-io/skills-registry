---
name: planning-workflow
description: Create structured, language-agnostic implementation plans with risks, tests, and approvals. Use when the user asks for a plan or design before coding, especially for large new features or brand new projects where SDLC phases should be explicit. Use when this capability is needed.
metadata:
  author: wdonofrio
---

# Planning Workflow

Use this for design and planning requests. Plans should be concrete, ordered, and grounded in the actual codebase.

## When to Plan

- New features with multiple components
- Significant refactoring
- Changes affecting multiple systems
- Unfamiliar codebase or technology
- When the user explicitly asks for a plan

## Planning Process

### Step 1: Clarify Requirements

Before planning, ensure you understand:
- **Goal**: What problem are we solving?
- **Scope**: What's in and out of scope?
- **Constraints**: Time, technology, compatibility requirements?
- **Success criteria**: How do we know when it's done?

Ask for missing details. Don't assume.

### Step 2: Research the Codebase

- Find existing patterns and conventions
- Identify code that will be modified
- Look for similar implementations to follow
- Note dependencies and potential conflicts

### Step 3: Create the Plan

Structure the plan with:

#### Overview
- One sentence: what we're building and why
- Key decisions made

#### Implementation Steps
Ordered list of concrete tasks:
1. What to do
2. Which files/components affected
3. Any dependencies on other steps

#### For Large Projects (SDLC Phases)

For major features or new projects, structure around:

1. **Requirements**: Document detailed requirements
2. **Design**: API contracts, data models, component structure
3. **Implementation**: Ordered build steps
4. **Testing**: Test strategy, coverage expectations
5. **Deployment**: Rollout plan, feature flags, monitoring
6. **Documentation**: User docs, API docs, runbooks

### Step 4: Identify Risks

For each significant risk:
- What could go wrong?
- What's the impact?
- How do we mitigate it?

Common risks:
- Breaking existing functionality
- Performance degradation
- Security vulnerabilities
- Data migration issues
- Integration failures

### Step 5: Define Test Strategy

- What tests will be added?
- What existing tests might need updates?
- What manual testing is needed?
- What's the acceptance criteria?

### Step 6: Note Documentation Updates

- README changes?
- API documentation?
- User-facing documentation?
- Architecture decision records?

### Step 7: Request Approval

Explicitly ask for approval before implementing. Include:
- Summary of the approach
- Key decisions and trade-offs
- Any alternatives considered
- What feedback you need

## Plan Template

```markdown
## Overview
[One sentence summary]

## Steps
1. [Step 1]
   - Files: [files affected]
   - Depends on: [prior steps]

2. [Step 2]
   ...

## Risks
- [Risk 1]: [Mitigation]

## Testing
- [ ] [Test case 1]
- [ ] [Test case 2]

## Documentation
- [ ] [Doc update 1]

## Questions for Review
- [Question 1]?
```

## Reminders

- Stay language-agnostic unless the codebase dictates otherwise
- Reference `shared/guidelines/` for security, testing, and style standards
- Include running tests in the plan
- Plans can be iterative—it's OK to revise after starting

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/wdonofrio) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
