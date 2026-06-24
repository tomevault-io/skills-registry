---
name: vitest-patterns
description: Vitest is a modern testing framework designed for the Vite ecosystem, Use when this capability is needed.
metadata:
  author: amnadtaowsoam
---

# Vitest Patterns

## Skill Profile
*(Select at least one profile to enable specific modules)*
- [ ] **DevOps**
- [x] **Backend**
- [ ] **Frontend**
- [ ] **AI-RAG**
- [ ] **Security Critical**

## Overview
Vitest is a modern testing framework designed for the Vite ecosystem, but works with any project. It's 10-20x faster than Jest, with native TypeScript/ESM support and Jest-compatible API, making migration easy. This skill covers Vitest setup, basic testing, mocking, React component testing, async testing, snapshot testing, test utilities, and running tests.

## Why This Matters
Vitest provides:
- **Speed**: 10-20x faster than Jest (Vite's transform pipeline)
- **Native ESM/TypeScript**: No transpilation config needed
- **Jest Compatible**: Same API, easy migration
- **Watch Mode**: Instant feedback with smart re-runs
- **UI Mode**: Visual test debugging interface

## Core Concepts
1. **Configuration**: Vite config integration
2. **Test Environment**: jsdom, node, happy-dom
3. **Globals**: describe, it, expect globally available
4. **Mocking**: vi.fn(), vi.mock(), vi.spyOn()
5. **Snapshots**: Snapshot testing for UI components
6. **Coverage**: Built-in coverage with v8 or istanbul
7. **Watch Mode**: Smart file watching and re-running

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
* **Depends on**: None
* **Compatible with**: None
* **Conflicts with**: None
* **Related Skills**: None

## Quick Start
#

## Assumptions
- Project uses Vite or compatible build tool
- TypeScript is configured
- Testing libraries are installed

## Compatibility
- **Node.js**: 16+
- **Vite**: 4+
- **TypeScript**: 4.5+
- **React**: 16.8+

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


## Agent Directives
1. Always clean up mocks
2. Use vi.clearAllMocks() between tests
3. Test one thing per test
4. Keep tests independent
5. Use descriptive test names
6. Review snapshots before committing

## Definition of Done (DoD) Checklist

- [ ] Tests passed + coverage met
- [ ] Lint/Typecheck passed
- [ ] Logging/Metrics/Trace implemented
- [ ] Security checks passed
- [ ] Documentation/Changelog updated
- [ ] Accessibility/Performance requirements met (if frontend)


## Anti-patterns
1. **Not Cleaning Up Mocks**
   ```typescript
   // Bad: No cleanup
   describe('Test', () => {
     it('test', () => {
       const mock = vi.fn();
       // ...
     });
   });

   // Good: Cleanup in afterEach
   describe('Test', () => {
     afterEach(() => {
       vi.clearAllMocks();
     });

     it('test', () => {
       const mock = vi.fn();
       // ...
     });
   });
   ```

2. **Testing Implementation Details**
   ```typescript
   // Bad: Testing internals
   it('should set internal state', () => {
     expect(component._state).toBe('value');
   });

   // Good: Testing behavior
   it('should display value', () => {
     expect(screen.getByText('value')).toBeInTheDocument();
   });
   ```

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
