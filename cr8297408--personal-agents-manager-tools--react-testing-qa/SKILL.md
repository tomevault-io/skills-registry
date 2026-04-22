---
name: react-testing-qa
description: > Use when this capability is needed.
metadata:
  author: cr8297408
---

# React Testing & QA Specialist

## 1. Overview
This skill specializes in ensuring the quality and reliability of React applications through automated testing. It enforces the "Testing Library" philosophy: **Test your software the way users use it.** It prioritizes Accessibility-first queries (`getByRole`), robust user event simulation (`user-event`), and proper testing of custom hooks.

## 2. Prerequisites & Context
*   **Required Tools**:
    *   `view_file`: To understand the component under test.
    *   `write_to_file`: To create or update test files (`.test.tsx`, `.spec.tsx`).
    *   `run_command`: To run the test suite (`npm test`).
*   **Environment**: Vitest (preferred) or Jest + React Testing Library.
*   **Input**:
    *   Component code.
    *   Expected behavior / requirements.

## 3. Workflow
1.  **Analyze Component**: identifying interactive elements (buttons, inputs) and visual states (loading, error, success).
2.  **Plan Test Scenarios**: Happy path, Error path, Edge cases.
3.  **Write Tests**:
    *   Render component.
    *   Find element by **Role** (Accessibility check).
    *   Interact (User Event).
    *   Assert result (DOM change, function call).
4.  **Check A11y**: Use `axe-core` or similar to verify no accessibility violations.

## 4. Detailed Instructions & Rules

### Critical Rules
-   [ ] **Rule 1**: **Test Behavior, Not Implementation**. Do NOT check internal state (`state.count`). Check the DOM (`getByText('Count: 1')`).
-   [ ] **Rule 2**: **Priority of Queries**. Always try `getByRole` first. Then `getByLabelText`, `getByPlaceholderText`. Avoid `getByTestId` unless absolutely necessary.
-   [ ] **Rule 3**: **User Event**. Use `@testing-library/user-event` over `fireEvent` for more realistic interactions.
-   [ ] **Rule 4**: **Async Utilities**. Used `waitFor`, `findBy...` for async updates. Never use `act()` manually if RTL wrappers handle it.
-   [ ] **Rule 5**: **Clean Setup**. Do not share mutable state between tests. Render fresh components for each test case.

### Formatting Guidelines
-   **Test Files**: `[Component].test.tsx` or `[Component].spec.tsx`.
-   **Structure**: `describe('Component', () => { it('should...', () => { ... }) })`.

## 5. Examples

### Example 1: Component Interaction Test
See [examples/component-test.md](examples/component-test.md).

### Example 2: Custom Hook Test
See [examples/hook-test.md](examples/hook-test.md).

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/cr8297408) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
