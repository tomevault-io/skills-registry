---
name: review-ui-accessibility
description: Review and improve UI component accessibility for WCAG compliance Use when this capability is needed.
metadata:
  author: luanps2
---

# Review UI Accessibility Skill

## Purpose

Ensure UI components meet WCAG 2.1 AA accessibility standards for inclusive user experiences.

## WCAG 2.1 AA Checklist

### Perceivable

- [ ] **Color Contrast**: Text has 4.5:1 contrast ratio (3:1 for large text)
- [ ] **Alt Text**: All images have descriptive alt attributes
- [ ] **Captions**: Videos have captions/subtitles
- [ ] **Text Resize**: Content readable at 200% zoom
- [ ] **Orientation**: Works in portrait and landscape

### Operable

- [ ] **Keyboard Access**: All interactive elements keyboard accessible
- [ ] **Focus Visible**: Clear focus indicators (outline, border, background)
- [ ] **No Keyboard Trap**: Users can navigate away with keyboard
- [ ] **Skip Links**: "Skip to main content" link present
- [ ] **Time Limits**: No time limits or can be extended
- [ ] **Animations**: Respects `prefers-reduced-motion`

### Understandable

- [ ] **Page Language**: `<html lang="en">` specified
- [ ] **Labels**: Form inputs have associated labels
- [ ] **Error Messages**: Clear, specific error messages
- [ ] **Instructions**: Clear instructions for complex interactions
- [ ] **Consistent Navigation**: Navigation consistent across pages

### Robust

- [ ] **Valid HTML**: No HTML validation errors
- [ ] **ARIA**: Proper ARIA roles, states, and properties
- [ ] **Screen Reader**: Tested with screen reader (NVDA, JAWS, VoiceOver)

## Common Accessibility Patterns

### Semantic HTML

```html
<!-- ❌ Bad: Non-semantic -->
<div class="button" onclick="submit()">Submit</div>

<!-- ✅ Good: Semantic -->
<button type="submit">Submit</button>
```

### Form Labels

```html
<!-- ❌ Bad: No label association -->
<label>Email</label>
<input type="email" />

<!-- ✅ Good: Associated label -->
<label for="email">Email</label>
<input type="email" id="email" name="email" />
```

### ARIA Labels

```html
<!-- Icon-only button -->
<button aria-label="Close dialog">
  <svg aria-hidden="true"><!-- X icon --></svg>
</button>

<!-- Loading state -->
<button aria-busy="true" aria-label="Submitting form">
  <span aria-hidden="true">Loading...</span>
</button>
```

### Keyboard Navigation

```tsx
function Dialog({ onClose }: DialogProps) {
  const dialogRef = useRef<HTMLDivElement>(null);

  useEffect(() => {
    // Trap focus inside dialog
    const handleKeyDown = (e: KeyboardEvent) => {
      if (e.key === 'Escape') {
        onClose();
      }
    };

    dialogRef.current?.focus();
    document.addEventListener('keydown', handleKeyDown);
    return () => document.removeEventListener('keydown', handleKeyDown);
  }, [onClose]);

  return (
    <div
      ref={dialogRef}
      role="dialog"
      aria-modal="true"
      aria-labelledby="dialog-title"
      tabIndex={-1}
    >
      <h2 id="dialog-title">Dialog Title</h2>
      {/* Dialog content */}
    </div>
  );
}
```

## Testing Tools

- **axe DevTools**: Browser extension for automated accessibility testing
- **WAVE**: Web Accessibility Evaluation Tool
- **Lighthouse**: Built into Chrome DevTools
- **NVDA/JAWS**: Screen reader testing (Windows)
- **VoiceOver**: Screen reader testing (macOS/iOS)

## Quick Fixes

1. **Add alt text**: `<img src="logo.png" alt="Company Logo" />`
2. **Add labels**: Associate labels with inputs using `for` and `id`
3. **Add keyboard support**: Ensure `onKeyDown` handles Enter/Space
4. **Improve contrast**: Use darker colors for text
5. **Add focus styles**: `.button:focus { outline: 2px solid blue; }`
6. **Add ARIA labels**: Use `aria-label` for icon buttons

## Related Skills

- `create_component` - Build accessible components from the start

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/luanps2) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
