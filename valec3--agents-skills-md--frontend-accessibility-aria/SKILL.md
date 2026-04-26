---
name: frontend-accessibility-aria
description: Standardized guidelines and patterns for Frontend Accessibility Aria. Use when this capability is needed.
metadata:
  author: valec3
---

# Frontend Accessibility Aria

## When to use this skill
- Making apps accessible
- Adding ARIA attributes
- Keyboard navigation
- Screen reader support

## Workflow
- [ ] Use semantic HTML first
- [ ] Add ARIA when needed
- [ ] Test with screen reader
- [ ] Ensure keyboard navigation
- [ ] Check color contrast

## Instructions

### Semantic HTML
```typescript
// ✅ Good
<button onClick={handleClick}>Click</button>

// ❌ Bad
<div onClick={handleClick}>Click</div>
```

### ARIA Attributes
```typescript
<button
  aria-label="Close dialog"
  aria-expanded={isOpen}
  aria-controls="dialog-content"
>
  ×
</button>

<div
  id="dialog-content"
  role="dialog"
  aria-labelledby="dialog-title"
  aria-modal="true"
>
  <h2 id="dialog-title">Title</h2>
</div>
```

### Skip Links
```typescript
<a href="#main-content" className="skip-link">
  Skip to main content
</a>
<main id="main-content">
  {/* Content */}
</main>
```

## Resources
- Semantic HTML first
- ARIA as enhancement
- Test with keyboard only
- Use axe DevTools

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/valec3) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
