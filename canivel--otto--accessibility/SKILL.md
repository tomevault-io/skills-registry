---
name: accessibility
description: Use when building or auditing UI for accessibility (a11y). Covers WCAG 2.1 AA compliance, semantic HTML, ARIA, keyboard navigation, focus management, color contrast, screen readers, and form accessibility.
metadata:
  author: canivel
---

# Accessibility (a11y) - WCAG 2.1 AA

## Semantic HTML First

Always use semantic HTML before reaching for ARIA. Native elements provide built-in keyboard support and screen reader announcements.

```tsx
// BAD: div soup with ARIA bolted on
<div role="button" tabIndex={0} onClick={handleClick} onKeyDown={handleKeyDown}>
  Save
</div>

// GOOD: use the native element
<button onClick={handleClick}>Save</button>

// BAD: clickable div for navigation
<div onClick={() => navigate('/settings')}>Settings</div>

// GOOD: anchor or button
<a href="/settings">Settings</a>

// Semantic structure
<header>...</header>
<nav aria-label="Main navigation">...</nav>
<main>
  <article>
    <h1>Page Title</h1>
    <section aria-labelledby="section-heading">
      <h2 id="section-heading">Section</h2>
    </section>
  </article>
</main>
<footer>...</footer>
```

## ARIA Attributes - Use When Native HTML Is Insufficient

```tsx
// Live regions for dynamic content updates
<div aria-live="polite" aria-atomic="true">
  {notification && <p>{notification.message}</p>}
</div>

// Expanded/collapsed state
<button aria-expanded={isOpen} aria-controls="menu-panel" onClick={toggle}>
  Menu
</button>
<div id="menu-panel" role="menu" hidden={!isOpen}>
  {menuItems}
</div>

// Loading states
<div aria-busy={isLoading} aria-live="polite">
  {isLoading ? <Spinner aria-label="Loading content" /> : <Content />}
</div>

// Dialog/modal
<div role="dialog" aria-modal="true" aria-labelledby="dialog-title">
  <h2 id="dialog-title">Confirm Delete</h2>
  <p>This action cannot be undone.</p>
  <button onClick={onConfirm}>Delete</button>
  <button onClick={onCancel}>Cancel</button>
</div>

// NEVER use aria-label when visible text already describes the element.
// NEVER use role="button" on a div when you can use <button>.
```

## Keyboard Navigation

```tsx
// All interactive elements must be keyboard accessible
// Native elements (<button>, <a>, <input>) get this for free

// Custom keyboard handling for composite widgets
function TabList({ tabs }: { tabs: Tab[] }) {
  const [activeIndex, setActiveIndex] = useState(0);

  const handleKeyDown = (e: React.KeyboardEvent) => {
    switch (e.key) {
      case 'ArrowRight':
        setActiveIndex((prev) => (prev + 1) % tabs.length);
        break;
      case 'ArrowLeft':
        setActiveIndex((prev) => (prev - 1 + tabs.length) % tabs.length);
        break;
      case 'Home':
        setActiveIndex(0);
        break;
      case 'End':
        setActiveIndex(tabs.length - 1);
        break;
    }
  };

  return (
    <div role="tablist" onKeyDown={handleKeyDown}>
      {tabs.map((tab, i) => (
        <button
          key={tab.id}
          role="tab"
          aria-selected={i === activeIndex}
          tabIndex={i === activeIndex ? 0 : -1}
          ref={el => { if (i === activeIndex) el?.focus(); }}
        >
          {tab.label}
        </button>
      ))}
    </div>
  );
}
```

## Focus Management

```tsx
// Trap focus in modals
function Modal({ isOpen, onClose, children }: ModalProps) {
  const modalRef = useRef<HTMLDivElement>(null);

  useEffect(() => {
    if (!isOpen) return;
    const previousFocus = document.activeElement as HTMLElement;
    modalRef.current?.focus();
    return () => previousFocus?.focus(); // restore focus on close
  }, [isOpen]);

  return isOpen ? (
    <FocusTrap>
      <div ref={modalRef} role="dialog" aria-modal="true" tabIndex={-1}>
        {children}
        <button onClick={onClose}>Close</button>
      </div>
    </FocusTrap>
  ) : null;
}

// Focus visible styles - never remove the outline without a replacement
// BAD: *:focus { outline: none; }
// GOOD: use :focus-visible for keyboard-only focus rings
button:focus-visible {
  outline: 2px solid #4F46E5;
  outline-offset: 2px;
}
```

## Skip Links

```tsx
// Allow keyboard users to skip repetitive navigation
function SkipLink() {
  return (
    <a
      href="#main-content"
      className="sr-only focus:not-sr-only focus:absolute focus:top-4 focus:left-4 focus:z-50 focus:bg-white focus:px-4 focus:py-2"
    >
      Skip to main content
    </a>
  );
}

// In layout
<body>
  <SkipLink />
  <header><nav>...</nav></header>
  <main id="main-content" tabIndex={-1}>
    {children}
  </main>
</body>
```

## Color Contrast

```
WCAG 2.1 AA requirements:
- Normal text: minimum contrast ratio 4.5:1
- Large text (18pt+ or 14pt+ bold): minimum contrast ratio 3:1
- UI components and graphical objects: minimum contrast ratio 3:1

Tools to check:
- Chrome DevTools color picker (shows contrast ratio)
- axe DevTools browser extension
- WebAIM Contrast Checker: https://webaim.org/resources/contrastchecker/
```

```tsx
// Never rely on color alone to convey information
// BAD: only color indicates error
<input style={{ borderColor: hasError ? 'red' : 'gray' }} />

// GOOD: color + icon + text
<div>
  <input aria-invalid={hasError} aria-describedby={hasError ? 'error-msg' : undefined} />
  {hasError && (
    <p id="error-msg" role="alert" className="text-red-600">
      <ErrorIcon aria-hidden="true" /> Email is required
    </p>
  )}
</div>
```

## Form Accessibility

```tsx
// Every input must have a visible label
<label htmlFor="email">Email address</label>
<input id="email" type="email" required aria-required="true" />

// Group related fields
<fieldset>
  <legend>Shipping Address</legend>
  <label htmlFor="street">Street</label>
  <input id="street" />
  <label htmlFor="city">City</label>
  <input id="city" />
</fieldset>

// Error messages must be associated with inputs
<label htmlFor="password">Password</label>
<input id="password" type="password" aria-invalid={!!error} aria-describedby="password-error" />
{error && <p id="password-error" role="alert">{error}</p>}

// Descriptions and hints
<label htmlFor="username">Username</label>
<input id="username" aria-describedby="username-hint" />
<p id="username-hint">Must be 3-20 characters, letters and numbers only.</p>
```

## Screen Reader Testing

```bash
# Test with real screen readers:
# - macOS: VoiceOver (Cmd+F5)
# - Windows: NVDA (free) or JAWS
# - Mobile: TalkBack (Android), VoiceOver (iOS)

# Automated testing with jest-axe
import { axe, toHaveNoViolations } from 'jest-axe';
expect.extend(toHaveNoViolations);

it('has no accessibility violations', async () => {
  const { container } = render(<LoginForm />);
  expect(await axe(container)).toHaveNoViolations();
});
```

## Images and Media

```tsx
// Informative images must have alt text
<img src="/chart.png" alt="Sales increased 25% from Q1 to Q2 2024" />

// Decorative images use empty alt
<img src="/divider.svg" alt="" role="presentation" />

// Icons with meaning need accessible labels
<button aria-label="Close dialog"><XIcon aria-hidden="true" /></button>

// Video: provide captions and transcripts
<video controls>
  <source src="demo.mp4" type="video/mp4" />
  <track kind="captions" src="captions.vtt" srcLang="en" label="English" default />
</video>
```

## Anti-Patterns

- NEVER use `tabIndex` values greater than 0. It disrupts natural tab order.
- NEVER hide focus indicators without providing a visible alternative.
- NEVER use ARIA to override correct native semantics.
- NEVER use `placeholder` as a substitute for `<label>`. Placeholders disappear on input.
- NEVER autofocus inputs unless there is a clear UX reason (e.g., search page).
- NEVER use `aria-hidden="true"` on focusable elements. It makes them invisible but still tabbable.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/canivel) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
