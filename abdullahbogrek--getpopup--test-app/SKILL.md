---
name: test-app
description: Systematically test the modal.cards application - run unit tests, component tests, and E2E tests. Use when testing components, validating functionality, checking broken features, or running the full test suite. Use when this capability is needed.
metadata:
  author: abdullahbogrek
---

# modal.cards Test Agent

Systematically test the modal.cards Next.js application.

## Project Context

- **Framework**: Next.js 12.3.1 + React 18 + TypeScript
- **Styling**: Tailwind CSS + SCSS
- **State**: React Context (ModalContext, Modal1Context, Modal2Context, LanguageContext)
- **Test Runner**: Jest 29 + React Testing Library 14 + Playwright
- **Dev Server**: `npm run dev` (port 3000 or 3001)

## Test Infrastructure

- Config: `jest.config.js`, `playwright.config.ts`
- Setup: `jest.setup.js` (loads @testing-library/jest-dom)
- Custom render with providers: `test-utils/render.tsx`
- Unit tests: `__tests__/` directory
- E2E tests: `e2e/` directory

## Commands

```bash
# Run all unit tests
npm test

# Run specific test file
npx jest __tests__/components/Modal1.test.tsx

# Run tests matching a pattern
npx jest --testPathPattern="Generator"

# Run with coverage
npm run test:coverage

# Run E2E tests
npm run test:e2e
```

## Testing Workflow

When invoked with `/test-app`, follow this workflow:

### 1. If argument is `all` or no argument:
- Run `npm test` to execute all unit/component tests
- Analyze failures and report results
- Categorize issues: broken functionality, missing implementations, UI bugs

### 2. If argument is `unit`:
- Run only Jest unit tests: `npm test`
- Report pass/fail summary

### 3. If argument is `e2e`:
- Ensure dev server is running on localhost
- Run `npm run test:e2e`
- Report results with screenshots on failure

### 4. If argument is a component name (e.g., `Modal1`, `Generator`):
- Find and run tests matching that component: `npx jest --testPathPattern="$ARGUMENTS"`
- If no tests exist, inform the user and offer to create them

## Test Writing Guidelines

When writing new tests, always:

1. **Import render from** `test-utils/render.tsx` (NOT from @testing-library/react directly)
2. **Use userEvent** over fireEvent for interactions
3. **Query by accessibility**: getByRole, getByText, getByLabelText > getByTestId
4. **Test behavior**, not implementation details
5. **Follow AAA pattern**: Arrange, Act, Assert

### Example Test Structure

```tsx
import { render, screen } from '../../test-utils/render';
import userEvent from '@testing-library/user-event';
import ComponentName from '../../components/ComponentName';

describe('ComponentName', () => {
  it('should render correctly', () => {
    render(<ComponentName />);
    expect(screen.getByText('Expected text')).toBeInTheDocument();
  });

  it('should handle user interaction', async () => {
    const user = userEvent.setup();
    render(<ComponentName />);
    await user.click(screen.getByRole('button', { name: /click me/i }));
    expect(screen.getByText('Result')).toBeInTheDocument();
  });
});
```

## Known Application Issues to Test For

- Modal2-Modal13 are mostly hardcoded (no context binding)
- Modal2Context exists but is not wired into _app.tsx providers
- "Get your Code" button in Settings has empty handler
- Webhook integration is not implemented
- HTML code output generation is missing
- SVG attributes use HTML syntax (stroke-linecap) instead of React syntax (strokeLinecap)
- Some inputs have hardcoded `value` prop preventing user typing (Targeting section)

## Reporting

After running tests, provide a structured report:

```
## Test Results Summary

**Passed**: X | **Failed**: Y | **Skipped**: Z

### Failures
- [ComponentName] Description of failure
- [ComponentName] Description of failure

### Identified Issues
1. **Critical**: Issues that break core functionality
2. **Major**: Features that don't work as expected
3. **Minor**: UI inconsistencies, warnings

### Recommendations
- Prioritized list of fixes needed
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/abdullahbogrek) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
