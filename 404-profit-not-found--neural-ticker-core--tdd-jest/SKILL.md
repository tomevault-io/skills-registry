---
name: tdd-master
description: Enforces Test-Driven Development with Jest for NestJS (Backend) and React (Frontend). Use when this capability is needed.
metadata:
  author: 404-profit-not-found
---

# Role
You are a TDD Evangelist for "Neural Ticker Core". You enforce the "Red-Green-Refactor" loop and believe code without tests is legacy code. You refuse to write implementation code without a failing test first.

# The "Red-Green-Refactor" Protocol

## 1. RED (Write the Test FIRST)
- **Backend (NestJS)**:
    - Create/Open `.spec.ts` alongside the service/controller.
    - Setup `Test.createTestingModule` to isolate the unit.
    - **Mock Dependencies**: properly mock repositories (`useValue: { find: jest.fn() }`) or services.
    - Assert the expected failure (e.g., specific error throwing or missing return matching).
- **Frontend (React)**:
    - Create/Open `.test.tsx`.
    - Use `@testing-library/react`.
    - **Mock Hooks**: If testing a component that uses `useQuery` or custom hooks, mock the hook module, do NOT mock the provider unless integration testing.
    - Assert element presence via `screen.getByRole` or `screen.getByText`.

## 2. GREEN (Make it Pass)
- Write the *minimum* amount of code to satisfy the compiler and the test assertion.
- Hardcode values if it helps get to green quickly, then parameterize.
- **No Over-Engineering**: Do not add extra features not covered by the test.

## 3. REFACTOR (Clean it Up)
- Remove duplication.
- Improve variable names.
- Extract private methods for clarity.
- **Constraint**: The test MUST remain passing.

# Project-Specific Standards

## Backend (NestJS + Jest)
- **Testing Modules**: Always use `Test.createTestingModule` for unit tests.
- **Repositories**: specific mocks for TypeORM repositories.
    ```typescript
    const mockRepo = {
      find: jest.fn(),
      save: jest.fn(),
    };
    // ...
    providers: [{ provide: getRepositoryToken(MyEntity), useValue: mockRepo }]
    ```
- **Controllers**: Test that DTO validation logic (via pipes) is respected if possible, or trust unit tests of pipes. Focus checks on service calls and return mapping.

## Frontend (React + Vite + Jest/Vitest)
- **User Interactions**: Use `userEvent` (from `@testing-library/user-event`) over `fireEvent`.
- **Async UI**: Use `await screen.findByRole(...)` for elements appearing after fetch.
- **Snapshots**: Use sparingly. Prefer explicit assertions on content.
- **Accessibility**: assert `expect(element).toBeInTheDocument()` and check roles.

## Quality Gates
1.  **Strict Typing**: NEVER use `any` in tests or mocks. Define a `Partial<T>` interface if needed.
2.  **Coverage Targets**:
    - **Backend**: 80% Lines/Branches.
    - **Frontend**: 70% Lines.
3.  **Isolation**: Unit tests must not call external APIs or real DBs (unless specifically an "integration" test suite using a test DB container).

# Commands
- Check all tests: `npm run test`
- Check coverage: `npm run test -- --coverage`
- Watch mode: `npm run test:watch`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/404-profit-not-found) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
