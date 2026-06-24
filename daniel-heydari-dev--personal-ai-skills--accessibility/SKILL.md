---
name: accessibility
description: Build inclusive web applications for all users Use when this capability is needed.
metadata:
  author: daniel-heydari-dev
---

# Skill: Accessibility

Build web applications that everyone can use, including people with disabilities.

## Semantic HTML

### Rules

- ✅ DO: Use semantic elements (`<header>`, `<nav>`, `<main>`, `<article>`)
- ✅ DO: Use proper heading hierarchy (h1 → h2 → h3)
- ✅ DO: Use `<button>` for actions, `<a>` for navigation
- ❌ DON'T: Use `<div>` for everything
- ❌ DON'T: Skip heading levels

### Examples

```html
<!-- ❌ Bad - div soup -->
<div class="header">
  <div class="nav">
    <div onclick="navigate()">Home</div>
  </div>
</div>
<div class="content">
  <div class="title">Welcome</div>
</div>

<!-- ✅ Good - semantic HTML -->
<header>
  <nav aria-label="Main navigation">
    <a href="/">Home</a>
    <a href="/about">About</a>
  </nav>
</header>
<main>
  <h1>Welcome</h1>
  <article>
    <h2>Latest News</h2>
    <p>Content...</p>
  </article>
</main>
<footer>
  <p>© 2024 Company</p>
</footer>
```

## Keyboard Navigation

### Rules

- ✅ DO: Make all interactive elements focusable
- ✅ DO: Use visible focus indicators
- ✅ DO: Support standard keyboard patterns (Tab, Enter, Escape, Arrow keys)
- ✅ DO: Manage focus when content changes
- ❌ DON'T: Remove focus outlines without replacement
- ❌ DON'T: Create keyboard traps

### Examples

```css
/* ❌ Bad - removes focus indicator */
*:focus {
  outline: none;
}

/* ✅ Good - visible focus indicator */
:focus-visible {
  outline: 2px solid var(--focus-color);
  outline-offset: 2px;
}

/* ✅ Good - skip link */
.skip-link {
  position: absolute;
  top: -40px;
}
.skip-link:focus {
  top: 0;
}
```

```typescript
// ✅ Good - keyboard support for custom component
function Menu({ items }: { items: MenuItem[] }) {
  const [activeIndex, setActiveIndex] = useState(0);

  const handleKeyDown = (e: KeyboardEvent) => {
    switch (e.key) {
      case 'ArrowDown':
        e.preventDefault();
        setActiveIndex(i => Math.min(i + 1, items.length - 1));
        break;
      case 'ArrowUp':
        e.preventDefault();
        setActiveIndex(i => Math.max(i - 1, 0));
        break;
      case 'Escape':
        closeMenu();
        break;
    }
  };

  return (
    <ul role="menu" onKeyDown={handleKeyDown}>
      {items.map((item, index) => (
        <li
          key={item.id}
          role="menuitem"
          tabIndex={index === activeIndex ? 0 : -1}
        >
          {item.label}
        </li>
      ))}
    </ul>
  );
}
```

## ARIA

### Rules

- ✅ DO: Use ARIA to enhance, not replace, semantic HTML
- ✅ DO: Use landmarks (`role="main"`, `role="navigation"`)
- ✅ DO: Use live regions for dynamic content
- ✅ DO: Label all interactive elements
- ❌ DON'T: Use ARIA incorrectly (worse than no ARIA)
- ❌ DON'T: Use `role="button"` on `<div>` (use `<button>`)

### Examples

```html
<!-- ✅ Good - labeled form -->
<form>
  <label for="email">Email address</label>
  <input
    type="email"
    id="email"
    aria-describedby="email-hint"
    aria-required="true"
  />
  <p id="email-hint">We'll never share your email.</p>
</form>

<!-- ✅ Good - icon button with label -->
<button aria-label="Close dialog">
  <svg aria-hidden="true"><!-- icon --></svg>
</button>

<!-- ✅ Good - live region for updates -->
<div aria-live="polite" aria-atomic="true">
  {message &&
  <p>{message}</p>
  }
</div>

<!-- ✅ Good - custom combobox -->
<div
  role="combobox"
  aria-expanded="{isOpen}"
  aria-haspopup="listbox"
  aria-controls="options-list"
>
  <input aria-autocomplete="list" aria-activedescendant="{activeOption}" />
  <ul id="options-list" role="listbox">
    {options.map(opt => (
    <li
      key="{opt.id}"
      role="option"
      aria-selected="{opt.id"
      =""
      =""
      ="selected}"
    >
      {opt.label}
    </li>
    ))}
  </ul>
</div>
```

## Images & Media

### Rules

- ✅ DO: Provide alt text for meaningful images
- ✅ DO: Use empty alt (`alt=""`) for decorative images
- ✅ DO: Provide captions/transcripts for video/audio
- ✅ DO: Don't rely on color alone to convey information
- ❌ DON'T: Use "image of" in alt text
- ❌ DON'T: Leave alt text empty for informative images

### Examples

```html
<!-- ✅ Good - meaningful alt text -->
<img src="chart.png" alt="Sales increased 25% from Q1 to Q2 2024" />

<!-- ✅ Good - decorative image -->
<img src="decoration.svg" alt="" />

<!-- ✅ Good - complex image with longer description -->
<figure>
  <img
    src="diagram.png"
    alt="System architecture"
    aria-describedby="diagram-desc"
  />
  <figcaption id="diagram-desc">
    The system consists of three layers: presentation (React), business logic
    (Node.js), and data (PostgreSQL).
  </figcaption>
</figure>

<!-- ✅ Good - video with captions -->
<video controls>
  <source src="video.mp4" type="video/mp4" />
  <track kind="captions" src="captions.vtt" srclang="en" label="English" />
</video>
```

## Forms

### Rules

- ✅ DO: Label all form inputs
- ✅ DO: Group related fields with `<fieldset>` and `<legend>`
- ✅ DO: Provide clear error messages
- ✅ DO: Associate errors with inputs
- ❌ DON'T: Use placeholder as only label
- ❌ DON'T: Rely on color alone for errors

### Examples

```html
<!-- ✅ Good - accessible form -->
<form>
  <fieldset>
    <legend>Shipping Address</legend>

    <div>
      <label for="street">Street address</label>
      <input
        type="text"
        id="street"
        aria-invalid={!!errors.street}
        aria-describedby={errors.street ? 'street-error' : undefined}
      />
      {errors.street && (
        <p id="street-error" role="alert" class="error">
          {errors.street}
        </p>
      )}
    </div>

    <div>
      <label for="city">City</label>
      <input type="text" id="city" required />
    </div>
  </fieldset>

  <button type="submit">Submit</button>
</form>
```

## Color & Contrast

### Rules

- ✅ DO: Maintain 4.5:1 contrast ratio for normal text
- ✅ DO: Maintain 3:1 contrast ratio for large text (18px+)
- ✅ DO: Don't rely on color alone to convey meaning
- ✅ DO: Test with color blindness simulators

### Examples

```css
/* ✅ Good - sufficient contrast */
:root {
  --text-primary: #1a1a1a; /* High contrast on white */
  --text-secondary: #595959; /* Still meets 4.5:1 */
  --background: #ffffff;
}

/* ✅ Good - not just color */
.error {
  color: #d32f2f;
  border-left: 3px solid #d32f2f;
}
.error::before {
  content: "⚠ ";
}
```

## Testing

### Tools

- **axe DevTools** — Browser extension for automated testing
- **WAVE** — Web accessibility evaluation tool
- **Lighthouse** — Accessibility audits
- **NVDA/VoiceOver** — Screen reader testing

### Checklist

- [ ] Navigate with keyboard only
- [ ] Test with screen reader
- [ ] Check color contrast
- [ ] Verify focus management
- [ ] Test at 200% zoom
- [ ] Run automated accessibility audit

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/daniel-heydari-dev) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
