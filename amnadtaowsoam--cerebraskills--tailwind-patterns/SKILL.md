---
name: tailwind-patterns
description: Tailwind CSS is a utility-first CSS framework that helps developers Use when this capability is needed.
metadata:
  author: amnadtaowsoam
---

# Tailwind Patterns

## Skill Profile
*(Select at least one profile to enable specific modules)*
- [ ] **DevOps**
- [x] **Backend**
- [ ] **Frontend**
- [ ] **AI-RAG**
- [ ] **Security Critical**

## Overview
Tailwind CSS is a utility-first CSS framework that helps developers build UI rapidly and maintainably using utility classes instead of custom CSS. This approach reduces context switching between HTML and CSS files. Tailwind CSS uses a utility-first approach which helps developers build UI rapidly with utility classes, maintain consistency with design tokens, create responsive layouts easily, support dark mode natively, fully customize through configuration files, and optimize for production with JIT mode that removes unused styles automatically.

## Why This Matters
- **Increases Development Velocity**: Reduces UI development time by 30-50%
- **Reduces CSS Bundle Size**: Purges unused styles, reducing bundle size by up to 80%
- **Increases Maintainability**: Consistent design tokens improve maintainability
- **Reduces Context Switching**: Utility classes reduce switching between HTML and CSS
- **Improves Consistency**: Consistent design system improves UX

---

## Core Concepts
#

## Inputs / Outputs / Contracts
* **Inputs**:
  - <e.g., env vars, request payload, file paths, schema>
* **Entry Conditions**:
  - <Pre-requisites: e.g., Repo initialized, DB running, specific branch checked out>
* **Outputs**:
  - <e.g., artifacts (PR diff, docs, tests, dashboard JSON)>
* **Artifacts Required (Deliverables)**:
  - <e.g., Code Diff, Unit Tests, Migration Script, API Docs>
* **Acceptance Evidence**:
  - <e.g., Test Report (screenshot/log), Benchmark Result, Security Scan Report>
* **Success Criteria**:
  - <e.g., p95 < 300ms, coverage ≥ 80%>

## Skill Composition
* **Depends on**: [typescript-standards](..\..\01-foundations\typescript-standards/SKILL.md)
* **Compatible with**: None
* **Conflicts with**: None
* **Related Skills**: [api-design](..\..\01-foundations\api-design/SKILL.md)

## Quick Start
1. **Install Tailwind CSS**: Install Tailwind and dependencies
2. **Initialize Configuration**: Create tailwind.config.js
3. **Add Directives**: Add Tailwind directives to CSS
4. **Customize Theme**: Extend theme with design tokens
5. **Build Components**: Build components using utility classes
6. **Add Responsive**: Add responsive modifiers
7. **Implement Dark Mode**: Add dark mode support
8. **Optimize Build**: Configure purge and optimization
9. **Document Patterns**: Document common patterns

```bash
# Install Tailwind CSS
npm install -D tailwindcss postcss autoprefixer

# Initialize Tailwind
npx tailwindcss init -p
```

```javascript
// tailwind.config.js
/** @type {import('tailwindcss').Config} */
module.exports = {
  content: [
    './src/**/*.{js,ts,jsx,tsx,mdx}',
    './app/**/*.{js,ts,jsx,tsx,mdx}',
  ],
  theme: {
    extend: {},
  },
  plugins: [],
}
```

```css
/* styles.css */
@tailwind base;
@tailwind components;
@tailwind utilities;
```

## Assumptions / Constraints / Non-goals

* **Assumptions**:
  - Development environment is properly configured
  - Required dependencies are available
  - Team has basic understanding of domain
* **Constraints**:
  - Must follow existing codebase conventions
  - Time and resource limitations
  - Compatibility requirements
* **Non-goals**:
  - This skill does not cover edge cases outside scope
  - Not a replacement for formal training


## Compatibility & Prerequisites

* **Supported Versions**:
  - Python 3.8+
  - Node.js 16+
  - Modern browsers (Chrome, Firefox, Safari, Edge)
* **Required AI Tools**:
  - Code editor (VS Code recommended)
  - Testing framework appropriate for language
  - Version control (Git)
* **Dependencies**:
  - Language-specific package manager
  - Build tools
  - Testing libraries
* **Environment Setup**:
  - `.env.example` keys: `API_KEY`, `DATABASE_URL` (no values)


## Test Scenario Matrix (QA Strategy)

| Type | Focus Area | Required Scenarios / Mocks |
| :--- | :--- | :--- |
| **Unit** | Core Logic | Must cover primary logic and at least 3 edge/error cases. Target minimum 80% coverage |
| **Integration** | DB / API | All external API calls or database connections must be mocked during unit tests |
| **E2E** | User Journey | Critical user flows to test |
| **Performance** | Latency / Load | Benchmark requirements |
| **Security** | Vuln / Auth | SAST/DAST or dependency audit |
| **Frontend** | UX / A11y | Accessibility checklist (WCAG), Performance Budget (Lighthouse score) |


## Technical Guardrails & Security Threat Model

### 1. Security & Privacy (Threat Model)
* **Top Threats**: Injection attacks, authentication bypass, data exposure
- [ ] **Data Handling**: Sanitize all user inputs to prevent Injection attacks. Never log raw PII
- [ ] **Secrets Management**: No hardcoded API keys. Use Env Vars/Secrets Manager
- [ ] **Authorization**: Validate user permissions before state changes

### 2. Performance & Resources
- [ ] **Execution Efficiency**: Consider time complexity for algorithms
- [ ] **Memory Management**: Use streams/pagination for large data
- [ ] **Resource Cleanup**: Close DB connections/file handlers in finally blocks

### 3. Architecture & Scalability
- [ ] **Design Pattern**: Follow SOLID principles, use Dependency Injection
- [ ] **Modularity**: Decouple logic from UI/Frameworks

### 4. Observability & Reliability
- [ ] **Logging Standards**: Structured JSON, include trace IDs `request_id`
- [ ] **Metrics**: Track `error_rate`, `latency`, `queue_depth`
- [ ] **Error Handling**: Standardized error codes, no bare except
- [ ] **Observability Artifacts**:
    - **Log Fields**: timestamp, level, message, request_id
    - **Metrics**: request_count, error_count, response_time
    - **Dashboards/Alerts**: High Error Rate > 5%


## Agent Directives & Error Recovery
*(ข้อกำหนดสำหรับ AI Agent ในการคิดและแก้ปัญหาเมื่อเกิดข้อผิดพลาด)*

- **Thinking Process**: Analyze root cause before fixing. Do not brute-force.
- **Fallback Strategy**: Stop after 3 failed test attempts. Output root cause and ask for human intervention/clarification.
- **Self-Review**: Check against Guardrails & Anti-patterns before finalizing.
- **Output Constraints**: Output ONLY the modified code block. Do not explain unless asked.


## Definition of Done (DoD) Checklist

- [ ] Tests passed + coverage met
- [ ] Lint/Typecheck passed
- [ ] Logging/Metrics/Trace implemented
- [ ] Security checks passed
- [ ] Documentation/Changelog updated
- [ ] Accessibility/Performance requirements met (if frontend)


## Anti-patterns
1. **Over-Engineering**: Using Tailwind for simple projects adds unnecessary complexity
2. **Poor Configuration**: Poor configuration leads to inconsistent styling
3. **Missing Purge**: Not purging unused styles causes large bundle sizes
4. **Inconsistent Design**: Inconsistent use of design tokens reduces maintainability
5. **Poor Performance**: Not optimizing bundle size causes slow load times
6. **Accessibility Issues**: Not considering accessibility limits user base
7. **Over-using Arbitrary Values**: Excessive arbitrary values reduce maintainability
8. **Not Organizing Classes**: Poorly organized classes are hard to read
9. **Ignoring Responsive**: Not considering responsive design causes poor UX
10. **Not Using @apply**: Not using @apply for repeated patterns causes code duplication

## Reference Links & Examples

* Internal documentation and examples
* Official documentation and best practices
* Community resources and discussions


## Versioning & Changelog

* **Version**: 1.0.0
* **Changelog**:
  - 2026-02-22: Initial version with complete template structure

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/amnadtaowsoam) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
