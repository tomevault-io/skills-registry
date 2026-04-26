---
name: atdd-assistant
description: | Use when this capability is needed.
metadata:
  author: valorvie
---

# ATDD Assistant

> **Language**: English | [繁體中文](../../../locales/zh-TW/skills/claude-code/atdd-assistant/SKILL.md)

**Version**: 1.0.0
**Last Updated**: 2026-01-19
**Applicability**: Claude Code Skills

---

## Purpose

This skill guides teams through the Acceptance Test-Driven Development workflow, helping them:
- Conduct effective Specification Workshops
- Write testable acceptance criteria in Given-When-Then format
- Convert criteria to executable acceptance tests
- Ensure proper collaboration between PO, Dev, and QA
- Integrate ATDD with BDD and TDD for complete workflow

---

## Quick Reference

### ATDD Workflow Checklist

```
┌─────────────────────────────────────────────────────────────────┐
│  🤝 SPECIFICATION WORKSHOP Phase                                 │
│  □ Product Owner presents user story                            │
│  □ Team asks clarifying questions                               │
│  □ Acceptance criteria defined together                         │
│  □ Concrete examples written for each AC                        │
│  □ Edge cases and error scenarios discussed                     │
│  □ Out of scope explicitly documented                           │
├─────────────────────────────────────────────────────────────────┤
│  🧪 DISTILLATION Phase                                           │
│  □ Examples converted to executable tests                       │
│  □ Ambiguity removed from tests                                 │
│  □ Tests in executable format (Gherkin, FitNesse, etc.)         │
│  □ Product Owner signs off on tests                             │
├─────────────────────────────────────────────────────────────────┤
│  💻 DEVELOPMENT Phase                                            │
│  □ Acceptance tests initially fail (RED)                        │
│  □ BDD used for feature tests, TDD for unit tests               │
│  □ Incremental progress toward passing ATs                      │
│  □ All acceptance tests pass (GREEN)                            │
│  □ Code refactored and clean                                    │
├─────────────────────────────────────────────────────────────────┤
│  🎬 DEMO Phase                                                   │
│  □ All acceptance tests passing                                 │
│  □ Demo environment prepared                                    │
│  □ Key stakeholders present                                     │
│  □ Product Owner validates functionality                        │
│  □ Story accepted or criteria refined                           │
└─────────────────────────────────────────────────────────────────┘
```

### Acceptance Criteria Quick Reference

| Element | Format | Example |
|---------|--------|---------|
| **User Story** | As a / I want / So that | As a customer, I want to reset my password, so that I can regain access |
| **AC Format** | Given / When / Then | Given I'm on the login page, When I click "Forgot Password", Then I should see a reset form |
| **Out of Scope** | Bullet list | - SMS reset, - Admin reset capability |
| **Technical Notes** | Bullet list | - Token expires in 24 hours |

### INVEST Criteria

| Principle | Description | Check |
|-----------|-------------|-------|
| **I**ndependent | Can be developed independently | No blocking dependencies |
| **N**egotiable | Details can be discussed | Not a contract |
| **V**aluable | Delivers business value | PO can explain the "why" |
| **E**stimable | Can be estimated | Team understands scope |
| **S**mall | Fits in one sprint | < 1 week of work |
| **T**estable | Can be verified | Clear acceptance criteria |

---

## Workflow Assistance

### Specification Workshop Guidance

When conducting a specification workshop:

1. **Story Presentation** (5 min)
   ```
   User Story: [Title]

   As a [role]
   I want [feature]
   So that [benefit]

   Business Value: [Why this matters]
   ```

2. **Clarifying Questions** (10 min)
   - Business: "What's the value?", "Who are the users?"
   - Development: "What's the impact?", "Dependencies?"
   - Testing: "What could go wrong?", "Edge cases?"

3. **AC Definition** (20 min)
   ```markdown
   ### AC-1: [Criterion name]
   **Given** [precondition]
   **When** [action]
   **Then** [expected result]
   ```

4. **Out of Scope** (10 min)
   - Explicitly list what is NOT included
   - Prevents scope creep during development

5. **Technical Notes** (5 min)
   - Implementation hints
   - Known constraints
   - Dependencies

### Distillation Guidance

When converting AC to executable tests:

1. **Review Each AC**
   - Is it unambiguous?
   - Can it be automated?
   - Does it verify business value?

2. **Choose Test Format**
   | Format | Best For |
   |--------|----------|
   | Gherkin | Behavior-focused, business-readable |
   | FitNesse | Data-driven, wiki tables |
   | Robot Framework | Complex workflows |
   | Code (xUnit) | Technical teams |

3. **Write Executable Tests**
   ```gherkin
   # For AC-1: Password reset request
   Scenario: Request password reset
     Given I am on the login page
     And I have a registered account
     When I click "Forgot Password"
     And I enter my email address
     Then I should see "Reset link sent"
     And I should receive an email within 5 minutes
   ```

4. **Get PO Sign-off**
   - PO confirms tests represent requirements
   - Sign-off before development starts

### Demo Guidance

When preparing for demo:

1. **Pre-Demo Checklist**
   ```
   □ All acceptance tests passing
   □ Demo environment ready
   □ Test data prepared
   □ Stakeholders notified
   ```

2. **Demo Structure** (15-30 min)
   - Context (1 min): Remind story and AC
   - Tests (2 min): Run acceptance tests live
   - Feature (5-10 min): Walk through each AC
   - Feedback (5 min): Gather feedback, Q&A

3. **Possible Outcomes**
   - ✅ Accepted: Story complete
   - 🔄 Refinement: Return to workshop
   - ❌ Rejected: Identify gaps

---

## User Story Template

```markdown
## User Story: [Title]

**As a** [role]
**I want** [feature]
**So that** [benefit]

## Acceptance Criteria

### AC-1: [Happy path]
**Given** [precondition]
**When** [action]
**Then** [expected result]

### AC-2: [Error scenario]
**Given** [precondition]
**When** [invalid action]
**Then** [error handling]

### AC-3: [Edge case]
**Given** [edge condition]
**When** [action]
**Then** [appropriate result]

## Out of Scope
- [Feature 1 not included]
- [Feature 2 deferred to future]

## Technical Notes
- [Implementation constraint]
- [Dependency information]
- [Performance requirement]

## Questions / Assumptions
- [Open question 1]
- [Assumption 1]
```

---

## Integration with Other Workflows

### ATDD → BDD → TDD Flow

```
ATDD Level (Business Acceptance)
  │
  │  User Story + Acceptance Criteria
  │  PO Sign-off
  │
  ▼
BDD Level (Behavior Specification)
  │
  │  Feature Files (Gherkin)
  │  Three Amigos collaboration
  │
  ▼
TDD Level (Implementation)
  │
  │  Unit Tests
  │  Red → Green → Refactor
  │
  ▼
Verification (Demo)
  │
  └──▶ PO Acceptance
```

### ATDD + SDD Integration

```markdown
# Link ATDD to SDD Spec

## User Story: US-123

**Spec Reference**: SPEC-001

### AC-1: Implements SPEC-001 Section 3.1
**Given** [from spec requirements]
**When** [action per spec]
**Then** [expected per spec]
```

---

## Configuration Detection

This skill supports project-specific configuration.

### Detection Order

1. Check `CONTRIBUTING.md` for "Disabled Skills" section
2. Check `CONTRIBUTING.md` for "ATDD Standards" section
3. Check for existing acceptance test patterns
4. If not found, **default to standard ATDD practices**

### First-Time Setup

If no configuration found:

1. Ask: "This project hasn't configured ATDD preferences. Which acceptance test format do you prefer?"
   - Gherkin (Cucumber, SpecFlow)
   - FitNesse tables
   - Code-based (xUnit)

2. After selection, suggest documenting in `CONTRIBUTING.md`:

```markdown
## ATDD Standards

### Acceptance Test Format
- Gherkin (Cucumber.js)

### User Story Template
- INVEST criteria required
- Given-When-Then format for AC

### Workflow
- Specification workshop required for all stories
- PO sign-off before development
- Demo for each completed story
```

---

## Detailed Guidelines

For complete standards, see:
- [ATDD Core Standard](../../../core/acceptance-test-driven-development.md)
- [ATDD Workflow Guide](./atdd-workflow.md)
- [Acceptance Criteria Guide](./acceptance-criteria-guide.md)

For related standards:
- [BDD Standards](../../../core/behavior-driven-development.md)
- [TDD Standards](../../../core/test-driven-development.md)
- [Testing Standards](../../../core/testing-standards.md)

---

## Anti-Patterns Quick Detection

| Symptom | Likely Problem | Quick Fix |
|---------|----------------|-----------|
| Features marked done but PO rejects | AC not validated with PO | Mandatory PO sign-off |
| Long dev with no progress | AC too large or vague | Break into smaller criteria |
| Acceptance tests always pass first time | Tests written after implementation | Tests before dev |
| Endless scope discussions | No "out of scope" definition | Explicit out-of-scope |
| AC can't be automated | QA/Dev not involved in AC definition | Technical perspective in workshop |

---

## RACI Matrix

| Activity | Product Owner | Developer | QA/Tester |
|----------|--------------|-----------|-----------|
| Define user story | **R/A** | C | C |
| Specification workshop | **R** | C | C |
| Define acceptance criteria | **A** | R | R |
| Write executable tests | C | R | **R/A** |
| Implement feature | C | **R/A** | C |
| Execute acceptance tests | I | R | **R/A** |
| Accept/reject feature | **R/A** | I | I |

**Legend**: R = Responsible, A = Accountable, C = Consulted, I = Informed

---

## Related Standards

- [Acceptance Test-Driven Development](../../../core/acceptance-test-driven-development.md) - Core ATDD standard
- [Behavior-Driven Development](../../../core/behavior-driven-development.md) - BDD standard
- [Test-Driven Development](../../../core/test-driven-development.md) - TDD standard
- [Spec-Driven Development](../../../core/spec-driven-development.md) - SDD workflow
- [Testing Standards](../../../core/testing-standards.md) - Testing framework
- [BDD Assistant](../bdd-assistant/SKILL.md) - BDD skill
- [TDD Assistant](../tdd-assistant/SKILL.md) - TDD skill

---

## Version History

| Version | Date | Changes |
|---------|------|---------|
| 1.0.0 | 2026-01-19 | Initial release |

---

## License

This skill is released under [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/).

**Source**: [universal-dev-standards](https://github.com/AsiaOstrich/universal-dev-standards)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/valorvie) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
