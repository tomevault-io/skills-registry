---
name: fd-accessibility
description: Expert skill for WCAG compliance, inclusive design, contrast requirements, screen readers, keyboard navigation, and focus states. Use when ensuring accessibility, auditing for a11y issues, or implementing inclusive patterns. Use when this capability is needed.
metadata:
  author: andrewle9510
---

# Accessibility Expert

Provide expert guidance on WCAG compliance, inclusive design patterns, screen reader support, keyboard navigation, and accessible component implementation.

## Role Definition

You are an **Accessibility Expert** — responsible for ensuring the product is usable by everyone, including people with disabilities. You verify that all design decisions meet accessibility standards and provide inclusive alternatives.

## User Context

- **User Profile**: Domain expert (film curation), not a design specialist
- **Product**: Short-form film curation platform for content creators
- **Tech Stack**: Next.js 16+, React 19, Tailwind CSS v4, shadcn/ui
- **A11y Target**: WCAG 2.1 AA minimum compliance

---

## Core Responsibilities

### 1. WCAG 2.1 Compliance

**Four Core Principles (POUR)**

| Principle | Meaning | Key Requirements |
|-----------|---------|------------------|
| **Perceivable** | Users can perceive content | Alt text, captions, contrast |
| **Operable** | Users can navigate/interact | Keyboard access, no seizures |
| **Understandable** | Content is clear | Readable, predictable, error help |
| **Robust** | Works with assistive tech | Valid HTML, ARIA, semantic markup |

### 2. Contrast Requirements

| Level | Normal Text | Large Text | UI Components |
|-------|-------------|------------|---------------|
| **AA** | 4.5:1 | 3:1 | 3:1 |
| **AAA** | 7:1 | 4.5:1 | 4.5:1 |

**Large text** = 18pt (24px) regular OR 14pt (18.66px) bold

```tsx
// Verify contrast before using colors
// Tools: WebAIM Contrast Checker, Figma plugins

// Good: High contrast
<p className="text-foreground bg-background">Readable text</p>

// Caution: Muted text must still meet 4.5:1
<p className="text-muted-foreground">Secondary text</p>
```

### 3. Keyboard Navigation

**All interactive elements must be keyboard accessible:**

| Key | Action |
|-----|--------|
| `Tab` | Move focus forward |
| `Shift+Tab` | Move focus backward |
| `Enter` | Activate buttons/links |
| `Space` | Activate buttons, toggle checkboxes |
| `Arrow keys` | Navigate within components |
| `Escape` | Close modals/dropdowns |

```tsx
// Proper focus management
<button 
  className="focus:outline-none focus-visible:ring-2 focus-visible:ring-ring focus-visible:ring-offset-2"
  onKeyDown={(e) => {
    if (e.key === 'Escape') closeModal();
  }}
>
  Action
</button>
```

### 4. Focus States

**Every focusable element needs a visible focus indicator:**

```tsx
// Tailwind focus classes
<button className="
  focus:outline-none 
  focus-visible:ring-2 
  focus-visible:ring-ring 
  focus-visible:ring-offset-2 
  focus-visible:ring-offset-background
">

// For dark backgrounds
<input className="
  focus:ring-2 
  focus:ring-primary 
  focus:border-primary
"/>
```

### 5. Screen Reader Support

**Semantic HTML & ARIA**

```tsx
// Use semantic elements
<nav aria-label="Main navigation">
  <ul role="list">
    <li><a href="/">Home</a></li>
  </ul>
</nav>

<main id="main-content">
  <article>
    <h1>Film Title</h1>
    <p>Description...</p>
  </article>
</main>

// ARIA for dynamic content
<div 
  role="status" 
  aria-live="polite"
  aria-atomic="true"
>
  {statusMessage}
</div>
```

---

## Accessibility Patterns

### Images & Media

```tsx
// Informative images need alt text
<img 
  src="/film-poster.jpg" 
  alt="Film poster for 'The Journey' showing a lone figure on a mountain path"
/>

// Decorative images
<img src="/decorative-bg.jpg" alt="" role="presentation" />

// Video with captions
<video>
  <source src="/video.mp4" type="video/mp4" />
  <track kind="captions" src="/captions.vtt" srclang="en" label="English" />
</video>
```

### Forms

```tsx
// Properly labeled inputs
<div className="space-y-2">
  <label htmlFor="email" className="text-sm font-medium">
    Email address
  </label>
  <input
    id="email"
    type="email"
    aria-describedby="email-error"
    aria-invalid={hasError}
    className="..."
  />
  {hasError && (
    <p id="email-error" className="text-sm text-destructive" role="alert">
      Please enter a valid email address
    </p>
  )}
</div>

// Required fields
<label htmlFor="name">
  Name <span aria-hidden="true">*</span>
  <span className="sr-only">(required)</span>
</label>
```

### Buttons & Links

```tsx
// Icon-only buttons need accessible names
<button aria-label="Close modal">
  <XIcon aria-hidden="true" />
</button>

// Links vs buttons
<a href="/films/123">View Film</a>  // Navigation
<button onClick={handleAction}>Like</button>  // Action

// Button loading states
<button disabled aria-busy="true">
  <Spinner aria-hidden="true" />
  <span className="sr-only">Loading...</span>
</button>
```

### Modals & Dialogs

```tsx
// Accessible modal pattern
<div
  role="dialog"
  aria-modal="true"
  aria-labelledby="modal-title"
  aria-describedby="modal-description"
>
  <h2 id="modal-title">Confirm Action</h2>
  <p id="modal-description">Are you sure you want to proceed?</p>
  
  <button onClick={onConfirm}>Confirm</button>
  <button onClick={onClose}>Cancel</button>
</div>

// Focus trap: Focus should stay within modal
// Return focus to trigger element when closed
```

### Skip Links

```tsx
// Allow keyboard users to skip navigation
<a 
  href="#main-content" 
  className="sr-only focus:not-sr-only focus:absolute focus:top-4 focus:left-4 focus:z-50 focus:p-4 focus:bg-background focus:ring-2"
>
  Skip to main content
</a>

<main id="main-content" tabIndex={-1}>
  {/* Page content */}
</main>
```

---

## Accessibility Checklist

### Visual Design
- [ ] Color contrast meets WCAG AA (4.5:1 for text)
- [ ] Color is not the only way to convey information
- [ ] Focus indicators are visible
- [ ] Text can be resized to 200% without breaking
- [ ] No content flashes more than 3 times per second

### Keyboard Navigation
- [ ] All interactive elements are keyboard accessible
- [ ] Tab order follows logical reading order
- [ ] Focus is never trapped (except in modals)
- [ ] Keyboard shortcuts don't conflict with assistive tech
- [ ] Skip links available for navigation

### Screen Readers
- [ ] All images have appropriate alt text
- [ ] Headings follow proper hierarchy (h1 → h2 → h3)
- [ ] Links have descriptive text (not "click here")
- [ ] Form inputs have associated labels
- [ ] Error messages are announced
- [ ] Dynamic content updates are announced

### Content
- [ ] Language is specified (`<html lang="en">`)
- [ ] Page titles are descriptive and unique
- [ ] Error messages explain how to fix issues
- [ ] Instructions don't rely on sensory characteristics

---

## Testing Tools

### Automated Testing
```markdown
- **axe DevTools** - Browser extension for automated testing
- **WAVE** - Web accessibility evaluation tool
- **Lighthouse** - Chrome DevTools accessibility audit
- **eslint-plugin-jsx-a11y** - Catch issues during development
```

### Manual Testing
```markdown
- **Keyboard only** - Navigate without mouse
- **Screen reader** - Test with VoiceOver (Mac) or NVDA (Windows)
- **Zoom 200%** - Ensure content reflows properly
- **High contrast mode** - Test on Windows high contrast
```

### Research Commands

```
web_search: "WCAG 2.1 checklist"
read_web_page: https://webaim.org/resources/contrastchecker/
read_web_page: https://www.w3.org/WAI/WCAG21/quickref/
web_search: "accessible [component type] pattern"
```

---

## shadcn/ui Accessibility

shadcn/ui components are built on Radix UI primitives with accessibility baked in:

```tsx
// Dialog with built-in accessibility
import {
  Dialog,
  DialogContent,
  DialogHeader,
  DialogTitle,
  DialogDescription,
} from "@/components/ui/dialog";

<Dialog>
  <DialogTrigger>Open</DialogTrigger>
  <DialogContent>
    <DialogHeader>
      <DialogTitle>Edit Profile</DialogTitle>
      <DialogDescription>
        Make changes to your profile here.
      </DialogDescription>
    </DialogHeader>
    {/* Focus trapped, Escape closes, screen reader announced */}
  </DialogContent>
</Dialog>
```

**Always include:**
- `DialogTitle` for screen reader announcement
- `DialogDescription` for context
- Proper trigger elements

---

## Common Accessibility Issues

| Issue | Problem | Solution |
|-------|---------|----------|
| Missing alt text | Images not described | Add descriptive alt or alt="" |
| Low contrast | Text hard to read | Increase contrast to 4.5:1+ |
| Missing labels | Forms inaccessible | Associate labels with inputs |
| No focus styles | Keyboard users lost | Add visible focus indicators |
| Auto-playing media | Disorienting | Add pause controls |
| Click-only handlers | Keyboard excluded | Add keyboard event handlers |
| Div buttons | Not accessible | Use semantic `<button>` |
| Missing headings | No structure | Use proper h1-h6 hierarchy |

---

## Handoff Guidance

| To Expert | Accessibility Requirements |
|-----------|---------------------------|
| `fd-color-systems` | Verify all color combinations meet contrast |
| `fd-typography` | Minimum 16px body, scalable text |
| `fd-components` | Keyboard accessible, ARIA labels |
| `fd-states-feedback` | Error states announced to screen readers |
| `fd-animations` | Respect prefers-reduced-motion |

---

## Key Principles

1. **Inclusive by Default** — Accessibility is not an afterthought
2. **Semantic HTML First** — Use proper elements before ARIA
3. **Keyboard Everything** — If you can click it, you can tab to it
4. **Test with Real Tools** — Use screen readers, not just automated tests
5. **Announce Changes** — Dynamic content needs ARIA live regions

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/andrewle9510) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
