---
name: creating-test-plans
description: Generates a comprehensive, professional test plan based on provided project documents or details. Automatically includes standard sections like scope, strategy, and risk assessment. Fills missing information with TBD/NA where necessary. Use when the user mentions "test plan", "testing strategy", "test roadmap", or "QA planning".
metadata:
  author: vikaskpawar
---

# Creating Test Plans

## When to use this skill
- Starting a new testing project and needing a standard document.
- Formalizing the testing approach for a specific feature or release.
- When the user provides a PRD, technical spec, or raw notes and asks for a testing strategy.
- When preparing for a code audit or compliance check that requires test documentation.

## Workflow
1. **Details Extraction**:
   - [ ] Analyze the provided document (PRD, Spec, or notes).
   - [ ] identify key features, stakeholders, and technical constraints.
2. **Template Generation**:
   - [ ] Create the Test Plan structure using the standard sections below.
   - [ ] Map extracted information into relevant sections.
3. **Gap Analysis**:
   - [ ] Identify missing information (e.g., specific dates, test environment URLs).
   - [ ] Mark missing fields as **[TBD]** or **[N/A]**.
4. **Validation**:
   - [ ] Ensure clear Entry and Exit criteria are defined.
   - [ ] Verify the Risk Assessment section addresses technical and business risks.

## Instructions

### 1. Mandatory Test Plan Sections
Every generated test plan must include these sections at a minimum:

- **Introduction**: Objective and Scope.
- **Test Strategy**: Types of testing (Unit, Integration, E2E, UI/UX).
- **Test Environment**: Hardware, software, and tools (e.g., Playwright, Vitest).
- **Test Schedule**: Key milestones and deliverables.
- **Roles & Responsibilities**: Who is responsible for what (Dev, QA, DevOps).
- **Risk Assessment**: Potential bottlenecks and mitigation strategies.
- **Entry & Exit Criteria**: When to start and when to stop testing.

### 2. Output Format
Use a professional, markdown-standard structure. 

```markdown
# Test Plan: [Project Name]

## 1. Introduction
- **Objective**: [Description]
- **Scope**: [What is in scope / what is out of scope]

## 2. Test Strategy
[Describe the levels of testing and tools to be used]

## 3. Test Tools & Environment
- **Framework**: [e.g., Playwright / TypeScript]
- **Browsers**: [e.g., Chromium, Firefox]
- **Environment**: [URL/Stage]

## 4. Risk Assessment
| Risk | Impact | Mitigation |
| :--- | :--- | :--- |
| [Risk 1] | [High/Mid/Low] | [TBD] |

## 5. Acceptance Criteria
- **Entry Criteria**: [Requirements to start]
- **Exit Criteria**: [Requirements to finish]
```

## Resources
- [Standard Test Plan Template](resources/test-plan-template.md)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/vikaskpawar) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
