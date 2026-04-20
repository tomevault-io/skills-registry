---
name: ui-development-workflow
description: This skill should be used when the user asks to "build UI", "implement design", "create component", "frontend", "Remix route", "React component", "create page", or when implementing user interfaces from design mocks. Guides through design-driven development with visual validation. Use when this capability is needed.
metadata:
  author: kaneshin
---

# UI Development Workflow Skill

## Purpose

Guide UI development using a design-driven workflow with visual validation. Ensure implementations match design mocks through iterative development and screenshot comparison using Playwright.

## When to Use This Skill

Apply this workflow when:
- Implementing UI from design mocks (Figma, screenshots, etc.)
- Building new pages or components with visual requirements
- Creating frontend features in Remix, React, or other frameworks
- Iterating on UI until it matches design specifications

## The UI Development Workflow

### Step 1: Receive Design Mock

Start with visual reference of what to build.

**Design sources:**
- Screenshot provided by user
- Figma link or design file
- Hand-drawn sketch or wireframe
- Verbal description with example references

**Clarify requirements:**
- Responsive behavior (mobile, tablet, desktop)
- Interactive states (hover, focus, disabled)
- Component variants or configurations
- Accessibility requirements

### Step 2: Implement in Framework

Build the UI component or page using the project's framework.

**Framework-specific implementation:**

**Remix routes:**
```typescript
// app/routes/dashboard.tsx
export default function Dashboard() {
  return (
    <div className="dashboard">
      <Header />
      <Sidebar />
      <MainContent />
    </div>
  );
}
```

**React components:**
```typescript
// components/Button.tsx
export function Button({ variant, children }) {
  return (
    <button className={`btn btn-${variant}`}>
      {children}
    </button>
  );
}
```

**Apply TDD principles to UI:**
- Write component tests first (rendering, interactions)
- Implement minimal component to pass tests
- Refactor for clarity and maintainability

**Styling approach:**
- Use project's styling system (Tailwind, CSS Modules, styled-components)
- Follow existing component patterns
- Match design system tokens (colors, spacing, typography)
- Implement responsive breakpoints

### Step 3: Visual Validation with Playwright

Use Playwright to capture screenshots and compare with design mocks.

**Load Playwright tools:**

Before taking screenshots, ensure Playwright plugin is available. If needed, search for and load Playwright MCP tools using ToolSearch.

**Screenshot methods:**

**Full page screenshot:**
```
browser_take_screenshot - Captures entire page
```

**Element snapshot:**
```
browser_snapshot - Captures specific element with selector
```

**Interactive screenshot workflow:**

1. **Navigate to component:**
   - Start browser session
   - Navigate to development server URL
   - Wait for page to load

2. **Capture screenshot:**
   - Take screenshot of full page or specific element
   - Save with descriptive name

3. **Compare with design:**
   - Review screenshot against design mock
   - Identify differences (spacing, colors, typography, layout)
   - Note what needs adjustment

**Example Playwright workflow:**
```
1. browser_navigate to http://localhost:3000/dashboard
2. browser_wait_for selector: '[data-testid="dashboard"]'
3. browser_take_screenshot
4. Compare screenshot with design mock
5. Identify: spacing too tight, wrong font weight
```

### Step 4: Iterate and Refine

Based on screenshot comparison, refine implementation.

**Iteration cycle:**
1. Identify specific differences from design
2. Adjust code (spacing, colors, layout, etc.)
3. Take new screenshot
4. Compare again
5. Repeat 2-3 times until satisfied

**Common adjustments:**
- Fine-tune spacing (padding, margin, gap)
- Adjust typography (font-size, font-weight, line-height)
- Fix colors (exact hex values from design)
- Correct alignment and layout
- Refine interactive states

**Iteration limit:**
Aim for 2-3 iterations. Perfect pixel-matching is rarely needed—focus on:
- Overall visual fidelity
- Responsive behavior
- Interactive states working correctly
- Accessibility maintained

### Step 5: Commit

Once UI matches design requirements sufficiently, commit the implementation.

**Commit message format:**
```
Implement [component/page name] UI

- Build [component] matching design mock
- Add responsive styles for mobile/desktop
- Implement interactive states (hover, focus)
- Include accessibility attributes
```

**Before committing:**
- [ ] Component tests passing
- [ ] Visual comparison acceptable
- [ ] Responsive behavior verified
- [ ] Interactive states functional
- [ ] Accessibility checked

See Commit Discipline skill for full commit requirements.

## Responsive Design Patterns

**Mobile-first approach:**

```css
/* Base styles (mobile) */
.button {
  padding: 0.5rem 1rem;
  font-size: 0.875rem;
}

/* Tablet and up */
@media (min-width: 768px) {
  .button {
    padding: 0.75rem 1.5rem;
    font-size: 1rem;
  }
}

/* Desktop */
@media (min-width: 1024px) {
  .button {
    padding: 1rem 2rem;
  }
}
```

**Test responsive breakpoints:**
- Use browser_resize tool to test different viewport sizes
- Take screenshots at each breakpoint
- Verify layout adapts correctly

## Component Testing in UI Development

**Test component behavior, not implementation:**

```typescript
test('shouldRenderButtonWithCorrectLabel', () => {
  render(<Button>Click Me</Button>);
  expect(screen.getByRole('button')).toHaveTextContent('Click Me');
});

test('shouldCallOnClickWhenButtonClicked', () => {
  const handleClick = jest.fn();
  render(<Button onClick={handleClick}>Click</Button>);

  fireEvent.click(screen.getByRole('button'));

  expect(handleClick).toHaveBeenCalledTimes(1);
});

test('shouldApplyDisabledStateCorrectly', () => {
  render(<Button disabled>Click</Button>);
  expect(screen.getByRole('button')).toBeDisabled();
});
```

**Visual regression testing:**

For critical UI components, consider visual regression tests:
- Capture baseline screenshots
- Compare new screenshots against baseline
- Flag visual differences for review

## Accessibility Considerations

**While implementing UI:**

**Semantic HTML:**
```tsx
// Good: semantic elements
<nav>
  <ul>
    <li><a href="/home">Home</a></li>
  </ul>
</nav>

// Avoid: div soup
<div className="nav">
  <div className="list">
    <div className="item" onClick={...}>Home</div>
  </div>
</div>
```

**ARIA attributes:**
```tsx
<button aria-label="Close dialog" onClick={onClose}>
  <CloseIcon />
</button>

<input
  type="text"
  aria-describedby="email-hint"
  aria-invalid={hasError}
/>
```

**Keyboard navigation:**
- All interactive elements keyboard accessible
- Focus visible on all focusable elements
- Logical tab order

**Color contrast:**
- Text meets WCAG AA standards (4.5:1 for normal text)
- Interactive elements distinguishable

**Test with browser tools:**
```
browser_evaluate to check accessibility tree
Verify focus management
Test keyboard navigation
```

## Complete UI Development Example

### Scenario: Build login form from design mock

**Step 1: Receive design**
User provides Figma link with login form design.

**Step 2: Implement**
```typescript
// app/routes/login.tsx
export default function Login() {
  return (
    <div className="min-h-screen flex items-center justify-center bg-gray-50">
      <form className="bg-white p-8 rounded-lg shadow-md w-full max-w-md">
        <h1 className="text-2xl font-bold mb-6">Sign In</h1>

        <div className="mb-4">
          <label htmlFor="email" className="block text-sm font-medium mb-2">
            Email
          </label>
          <input
            type="email"
            id="email"
            className="w-full px-3 py-2 border border-gray-300 rounded-md"
          />
        </div>

        <div className="mb-6">
          <label htmlFor="password" className="block text-sm font-medium mb-2">
            Password
          </label>
          <input
            type="password"
            id="password"
            className="w-full px-3 py-2 border border-gray-300 rounded-md"
          />
        </div>

        <button
          type="submit"
          className="w-full bg-blue-600 text-white py-2 rounded-md hover:bg-blue-700"
        >
          Sign In
        </button>
      </form>
    </div>
  );
}
```

**Step 3: Screenshot**
```
browser_navigate http://localhost:3000/login
browser_take_screenshot
```

**Compare:** Spacing looks tight, button color slightly off.

**Step 4: Iterate**

Iteration 1 - Adjust spacing:
```diff
-  <form className="bg-white p-8 rounded-lg shadow-md w-full max-w-md">
+  <form className="bg-white p-10 rounded-lg shadow-lg w-full max-w-md">
```

Iteration 2 - Fix button color:
```diff
-  className="w-full bg-blue-600 text-white py-2 rounded-md hover:bg-blue-700"
+  className="w-full bg-blue-500 text-white py-2.5 rounded-md hover:bg-blue-600"
```

Take screenshot again - looks good!

**Step 5: Commit**
```
Implement login form UI

- Build login form matching design specifications
- Add responsive styles with mobile-first approach
- Implement focus states for accessibility
- Include proper semantic HTML and labels
```

## Integration with TDD

Apply TDD to UI components:

**Test 1: Render form**
```typescript
test('shouldRenderLoginForm', () => {
  render(<Login />);
  expect(screen.getByRole('heading')).toHaveTextContent('Sign In');
  expect(screen.getByLabelText('Email')).toBeInTheDocument();
  expect(screen.getByLabelText('Password')).toBeInTheDocument();
  expect(screen.getByRole('button', { name: 'Sign In' })).toBeInTheDocument();
});
```

**Test 2: Handle submission**
```typescript
test('shouldCallOnSubmitWhenFormSubmitted', () => {
  const handleSubmit = jest.fn();
  render(<Login onSubmit={handleSubmit} />);

  fireEvent.click(screen.getByRole('button', { name: 'Sign In' }));

  expect(handleSubmit).toHaveBeenCalled();
});
```

Implement minimal code to pass tests, then refine styling to match design.

## Quick Reference

**UI Development Cycle:**
1. 📐 Receive design mock
2. 💻 Implement in framework (with TDD)
3. 📸 Screenshot with Playwright
4. 🔍 Compare with design
5. 🔄 Iterate 2-3 times
6. ✅ Commit when satisfied

**Before committing:**
- [ ] Component tests passing
- [ ] Visual fidelity acceptable
- [ ] Responsive at key breakpoints
- [ ] Interactive states working
- [ ] Accessibility verified

**Playwright tools:**
- `browser_navigate` - Go to URL
- `browser_take_screenshot` - Capture page
- `browser_snapshot` - Capture element
- `browser_resize` - Test responsive
- `browser_wait_for` - Wait for element

## Additional Resources

**Example files:**
- **`examples/ui-development-session.md`** - Complete UI development walkthrough with screenshots

For detailed Playwright usage and visual testing patterns, consult examples when needed.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/kaneshin) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
