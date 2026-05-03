---
name: frontend-code-review
description: Perform comprehensive frontend code reviews for JavaScript, TypeScript, React, Vue, Angular, and other frontend frameworks. Use this skill when reviewing pull requests, auditing existing codebases, or providing feedback on frontend code quality, performance, accessibility, and best practices. Use when this capability is needed.
metadata:
  author: pomelo-ccc
---

# Frontend Code Review Skill

This skill provides a systematic approach to reviewing frontend code, ensuring high-quality, maintainable, and performant web applications.

## When to Use

- Reviewing pull requests or merge requests
- Auditing existing frontend codebases
- Providing feedback on code quality
- Identifying potential bugs, security issues, or performance problems
- Ensuring consistency with team coding standards

## Review Dimensions

### 1. Code Quality & Readability

**Naming Conventions**
- Variables and functions use descriptive, meaningful names
- Components follow PascalCase (React/Vue/Angular)
- Utility functions follow camelCase
- Constants use UPPER_SNAKE_CASE
- Avoid abbreviations unless universally understood

**Code Organization**
- Files are appropriately sized (< 300 lines preferred)
- Related functionality is grouped together
- Clear separation of concerns (logic, UI, data)
- Consistent file and folder structure

**Comments & Documentation**
- Complex logic is explained with comments
- JSDoc/TSDoc for public APIs and utilities
- Avoid obvious comments that don't add value
- README files for modules and features

### 2. TypeScript / JavaScript Best Practices

**Type Safety (TypeScript)**
```typescript
// ❌ Avoid
const data: any = fetchData();
const items = data.list;

// ✅ Prefer
interface ApiResponse {
  list: Item[];
  total: number;
}
const data: ApiResponse = await fetchData();
const items = data.list;
```

**Modern Syntax**
- Use `const` and `let` instead of `var`
- Prefer template literals over string concatenation
- Use optional chaining (`?.`) and nullish coalescing (`??`)
- Destructuring for cleaner code
- Spread operator for immutable operations

**Error Handling**
- Proper try-catch blocks for async operations
- Graceful error states in UI
- Error boundaries in React
- Meaningful error messages

### 3. Component Architecture

**React Specific**
```tsx
// ❌ Avoid: Large monolithic components
function Dashboard() {
  // 500+ lines of mixed concerns
}

// ✅ Prefer: Composable, focused components
function Dashboard() {
  return (
    <DashboardLayout>
      <DashboardHeader />
      <DashboardMetrics />
      <DashboardCharts />
    </DashboardLayout>
  );
}
```

**State Management**
- Local state for component-specific data
- Context/Redux/Zustand for shared state
- Avoid prop drilling beyond 2-3 levels
- Memoization where appropriate (`useMemo`, `useCallback`)

**Props & Interfaces**
- Well-defined prop types/interfaces
- Required vs optional props clearly defined
- Default values for optional props
- Avoid passing entire objects when only specific fields needed

### 4. Performance

**Rendering Optimization**
```tsx
// ❌ Avoid: Unnecessary re-renders
function List({ items }) {
  const sorted = items.sort((a, b) => a.name.localeCompare(b.name));
  return sorted.map(item => <Item key={item.id} {...item} />);
}

// ✅ Prefer: Memoized computations
function List({ items }) {
  const sorted = useMemo(
    () => [...items].sort((a, b) => a.name.localeCompare(b.name)),
    [items]
  );
  return sorted.map(item => <Item key={item.id} {...item} />);
}
```

**Bundle Size**
- Tree-shakeable imports
- Dynamic imports for large dependencies
- Avoid importing entire libraries when only specific functions needed

**Network & Data**
- Debouncing for search inputs
- Pagination or virtualization for large lists
- Proper caching strategies
- Optimistic UI updates where appropriate

### 5. Accessibility (a11y)

**Semantic HTML**
```html
<!-- ❌ Avoid -->
<div onclick="handleClick()">Click me</div>

<!-- ✅ Prefer -->
<button type="button" onClick={handleClick}>Click me</button>
```

**ARIA & Keyboard Navigation**
- Proper ARIA labels and roles
- Focus management
- Keyboard navigation support
- Screen reader compatibility

**Visual Accessibility**
- Sufficient color contrast (WCAG AA minimum)
- Text scaling support
- Alternative text for images
- No reliance on color alone for information

### 6. Security

**XSS Prevention**
```tsx
// ❌ Avoid
<div dangerouslySetInnerHTML={{ __html: userContent }} />

// ✅ Prefer: Sanitize if HTML is required
import DOMPurify from 'dompurify';
<div dangerouslySetInnerHTML={{ __html: DOMPurify.sanitize(userContent) }} />
```

**Data Handling**
- Never expose sensitive data in client-side code
- Validate user inputs
- Secure API calls (HTTPS, proper headers)
- Content Security Policy awareness

### 7. Testing

**Test Coverage**
- Unit tests for utility functions
- Component tests for UI behavior
- Integration tests for user flows
- E2E tests for critical paths

**Test Quality**
```tsx
// ❌ Avoid: Testing implementation details
expect(wrapper.state('isOpen')).toBe(true);

// ✅ Prefer: Testing behavior
expect(screen.getByRole('dialog')).toBeVisible();
```

### 8. CSS & Styling

**Maintainability**
- Consistent naming convention (BEM, CSS Modules, etc.)
- CSS variables for theming
- Avoid inline styles except for dynamic values
- Mobile-first responsive design

**Performance**
- Avoid expensive selectors
- Minimize reflows and repaints
- Use GPU-accelerated properties for animations

## Review Checklist Template

```markdown
## Code Review: [PR/Feature Name]

### Summary
- [ ] Purpose of changes is clear
- [ ] Changes are appropriately scoped

### Code Quality
- [ ] Clear naming conventions
- [ ] Proper TypeScript types (no `any`)
- [ ] Consistent code style
- [ ] No dead code or console.logs

### Architecture
- [ ] Components are appropriately sized
- [ ] State management is correct
- [ ] Props are well-defined

### Performance
- [ ] No unnecessary re-renders
- [ ] Proper memoization where needed
- [ ] Efficient data structures

### Accessibility
- [ ] Semantic HTML
- [ ] Keyboard navigation
- [ ] ARIA attributes where needed

### Security
- [ ] No XSS vulnerabilities
- [ ] Sensitive data protected
- [ ] Inputs validated

### Testing
- [ ] Tests added/updated
- [ ] Edge cases covered

### Documentation
- [ ] Code comments for complex logic
- [ ] README updated if needed
```

## Review Communication Guidelines

**Be Constructive**
- Focus on the code, not the person
- Explain *why* something should change
- Provide examples or alternatives
- Acknowledge good patterns and improvements

**Prioritize Feedback**
- 🔴 **Blocker**: Must fix before merge (bugs, security, breaking changes)
- 🟡 **Suggestion**: Should fix (best practices, performance)
- 🟢 **Nitpick**: Optional (style preferences, minor improvements)

**Example Feedback Format**
```
🟡 **Suggestion**: Consider using `useMemo` here to avoid recalculating 
on every render. This becomes important as the list grows.

// Current
const filtered = items.filter(item => item.active);

// Suggested
const filtered = useMemo(() => items.filter(item => item.active), [items]);
```

## Framework-Specific Guidelines

### React
- Hooks rules compliance
- Proper useEffect dependencies
- Controlled vs uncontrolled components
- Context usage patterns

### Vue
- Composition API best practices
- Reactive reference handling
- Computed vs methods
- Proper v-model usage

### Angular
- Change detection strategy
- Proper use of observables
- NgModule organization
- Standalone components

## Output Format

When performing a code review, structure your response as:

1. **Overview**: Brief summary of what the code does
2. **Strengths**: What's done well
3. **Issues**: Problems found, categorized by severity
4. **Suggestions**: Recommendations for improvement
5. **Questions**: Clarifications needed (if any)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/pomelo-ccc) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
