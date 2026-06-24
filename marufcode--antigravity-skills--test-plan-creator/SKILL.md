---
name: test-plan-creator
description: This skill helps QA engineers and Product Owners generate professional, industry-standard 1-page test plans based on a specific high-quality template. Use it when starting a new project, feature, or version release to define objectives, scope, tools, and risks. Use when this capability is needed.
metadata:
  author: marufcode
---

# Test Plan Creator

## Purpose

To standardize and accelerate the creation of concise, effective test plans. This skill ensures that every project starts with a clear roadmap, covering objectives, scope, environment, and risks using a proven template that is both interviewer-friendly and project-ready.

## When to Use

Use this skill when:
- Kicking off a new testing phase for an application.
- Documenting the test strategy for a specific feature.
- Preparing for a technical interview or project presentation.
- Standardizing documentation across a QA team.

## Core Capabilities

1. **Information Gathering**: Prompts the user for key project details (Application Name, Tools, Environments, etc.).
2. **Template Application**: Injects gathered data into the predefined 1-page Test Plan template.
3. **Consistency Check**: Ensures all 8 essential sections of a test plan are addressed.
4. **Draft Generation**: Produces a ready-to-use Markdown file for Jira, GitHub, or internal documentation.

## Workflow

### Phase 1: Context Collection

Ask the user for the following details if not already provided in the prompt:
1. **Application Name**: The name of the software under test.
2. **Objective/Release Version**: What are we trying to achieve?
3. **Tools/Stack**: Which automation or manual tools are being used?
4. **Dates/Deadline**: When should the testing be completed?

### Phase 2: Refinement & Analysis

Analyze the user's input to suggest:
- **In Scope vs. Out of Scope** items (e.g., if it's a web app, suggest UI/API; if it's a mobile app, suggest Android/iOS).
- Common **Risks** based on the technology stack provided.

### Phase 3: Test Plan Generation

Generate the document using the following structure:

```markdown
🧪 TEST PLAN: <Application Name>

1. Objective
Verify that <Application Name> works as per requirements and is ready for release.

2. Scope
In Scope: Functional, Regression, Smoke, UI & API testing, Basic Security & Performance checks.
Out of Scope: Third-party systems, Production data validation.

3. Test Approach
Manual + Automation (Selenium / API), Agile-based execution, Risk-based testing.

4. Environment
QA / UAT. Browsers: Chrome, Firefox. Tools: <Tools Provided by User>.

5. Entry & Exit Criteria
Entry: Stable build, test data ready.
Exit: No critical defects, ≥95% pass rate.

6. Deliverables
Test Cases, Defect Report, Test Summary Report.

7. Risks
Requirement changes → frequent reviews. Environment issues → backup plan.

8. Approval
QA Lead & Product Owner sign-off.
```

## Quality Standards

- **Conciseness**: Keep the output to a single page (approximately 400-600 words).
- **Clarity**: Use bullet points and headers for readability.
- **Actionable**: Ensure the risks have corresponding mitigation strategies.

---

## References

- **ISTQB Standard Documents**: Standard definitions for Entry/Exit criteria.
- **Agile Testing Methodology**: Focus on iterative delivery.
- **Test Plan Template**: `resources/templates/test-plan-template.md`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/marufcode) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
