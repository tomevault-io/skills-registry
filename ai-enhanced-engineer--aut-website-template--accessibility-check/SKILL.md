---
name: accessibility-check
description: WCAG 2.1 AA compliance validation for static websites. Checks semantic HTML, ARIA labels, heading hierarchy, color contrast, keyboard navigation, and alt text. Use when user says "check accessibility", "a11y", "WCAG", or "screen reader". Use when this capability is needed.
metadata:
  author: ai-enhanced-engineer
---

# Accessibility Check Skill

## Purpose

Validate WCAG 2.1 Level AA compliance for static HTML websites through automated code inspection and manual check reminders.

---

## When to Use

**Trigger phrases**:
- "check accessibility"
- "a11y audit"
- "WCAG compliance"
- "screen reader test"
- "validate accessibility"
- "keyboard navigation check"

**Use cases**:
- Quick accessibility validation before deployment
- Targeted check after adding new content/sections
- Compliance verification for client deliverables
- Educational feedback for common a11y mistakes

---

## Automated Checks (Code Inspection)

### 1. Semantic HTML Structure

**Check for**:
- HTML5 landmarks: `<nav>`, `<main>`, `<section>`, `<article>`, `<aside>`, `<footer>`
- Proper document outline (not all `<div>` with classes)
- Lists use `<ul>`, `<ol>`, `<li>` (not `<div>` with bullet styling)
- Forms use `<form>`, `<label>`, `<input>`, `<button>`, `<fieldset>`

**Validation approach**:
```bash
# Search for divitis (overuse of divs where semantic HTML is better)
grep -n "<div class=\".*list" index.html  # Should use <ul>/<ol>
grep -n "<div class=\".*nav" index.html   # Should use <nav>
```

**Common violations**:
- Using `<div class="navigation">` instead of `<nav>`
- Using `<div>` for everything instead of semantic elements
- Missing `<main>` landmark

---

### 2. Heading Hierarchy

**Check for**:
- Single `<h1>` per page (page title)
- No skipped levels (h1 → h3 is invalid; must go h1 → h2 → h3)
- Logical nesting (sections have appropriate heading levels)

**Validation approach**:
```bash
# Extract all headings with line numbers
grep -n "<h[1-6]" index.html
```

**Analyze**:
- Count h1 tags (should be exactly 1)
- Check sequence doesn't skip levels
- Verify visual hierarchy matches DOM hierarchy

**Common violations**:
- Multiple `<h1>` tags
- Jumping from `<h2>` to `<h4>` (skipping h3)
- Using headings for visual styling instead of semantic structure

---

### 3. ARIA Labels & Roles

**Check for**:
- Icon-only buttons have `aria-label` or `aria-labelledby`
- Form inputs have labels (either `<label for="id">` or `aria-label`)
- Landmarks have implicit or explicit roles
- No redundant ARIA (e.g., `role="navigation"` on `<nav>` is redundant)

**Validation approach**:
```bash
# Find buttons without visible text (may need aria-label)
grep -n '<button[^>]*>' index.html

# Find inputs without labels
grep -n '<input' index.html

# Check for aria-label usage
grep -n 'aria-label' index.html
```

**Look for**:
- `<button class="icon-only">` without `aria-label`
- `<input>` without associated `<label for="...">` or `aria-label`
- Decorative images with missing `alt=""` or `role="presentation"`

**Common violations**:
- Hamburger menu button lacking accessible name
- Icon buttons (search, close, menu) without text alternatives
- Form inputs without labels

---

### 4. Alt Text for Images

**Check for**:
- All `<img>` tags have `alt` attribute
- Alt text is descriptive (not filename or "image")
- Decorative images use `alt=""` (empty alt)
- Complex images (charts, diagrams) have detailed descriptions

**Validation approach**:
```bash
# Find all images
grep -n '<img' index.html

# Find images without alt attribute (critical violation)
grep -n '<img[^>]*>' index.html | grep -v 'alt='
```

**Evaluate**:
- Is alt text present?
- Is it descriptive and meaningful?
- Decorative images should have empty alt: `alt=""`

**Common violations**:
- Missing `alt` attribute entirely
- Alt text like "image.png" or "picture"
- Logo images with `alt="logo"` instead of company name
- Decorative images with descriptive alt (should be empty)

---

### 5. Form Accessibility

**Check for**:
- Each `<input>`, `<textarea>`, `<select>` has associated `<label>`
- Labels use `for` attribute matching input `id`, or wrap the input
- Error messages are associated with fields (`aria-describedby`)
- Required fields marked with `required` attribute or `aria-required="true"`
- Fieldsets group related inputs (radio buttons, checkboxes)

**Validation approach**:
```bash
# Find all form inputs
grep -n '<input' index.html
grep -n '<label' index.html

# Check for required attributes
grep -n 'required' index.html
```

**Look for**:
- Input `id` matches label `for`
- Placeholder is NOT used as a label (fails WCAG)
- Error handling uses `aria-describedby` or adjacent text

**Common violations**:
- Using placeholder as label (disappears on focus)
- Missing labels entirely
- Labels not programmatically associated with inputs

---

### 6. Keyboard Navigation

**Automated checks** (limited):
```bash
# Check for focus styling removal (anti-pattern)
grep -n 'outline: none' styles/*.css
grep -n 'outline: 0' styles/*.css

# Check for positive tabindex (anti-pattern)
grep -n 'tabindex="[1-9]' index.html
```

**Manual testing required**:
- ⚠️ Tab through all interactive elements (links, buttons, forms)
- ⚠️ Verify focus indicators are visible
- ⚠️ Ensure tab order is logical (follows visual flow)
- ⚠️ Test keyboard shortcuts (Enter, Space, Escape)

**Common violations**:
- `outline: none` without custom focus indicator
- Positive `tabindex` values (disrupts natural tab order)
- Interactive elements not keyboard accessible (e.g., `<div onclick>`)

---

### 7. Color Contrast

**Automated checks** (limited):
```bash
# Extract colors from CSS
grep -n 'color:' styles/*.css
grep -n 'background-color:' styles/*.css
```

**Manual validation** (use contrast checker):
- Extract foreground and background color pairs
- Test contrast ratios using WebAim Contrast Checker or similar
  - Normal text: ≥ 4.5:1 (AA)
  - Large text (≥18pt or 14pt bold): ≥ 3:1 (AA)
  - UI components (buttons, icons): ≥ 3:1

**Use WebFetch** (optional):
```
WebFetch: https://webaim.org/resources/contrastchecker/?fcolor=FFFFFF&bcolor=6CB4EE
Prompt: What is the contrast ratio? Does it meet WCAG AA standards?
```

**Common violations**:
- Light gray text (#999) on white background
- Low-contrast button states (hover, active)
- Color-only indicators (links only distinguished by color)

---

## Output Format

```markdown
# Accessibility Check Report (WCAG 2.1 AA)

**Date**: [current date]
**File Reviewed**: index.html
**Overall Status**: ✅ PASS / ⚠️ ISSUES FOUND / ❌ CRITICAL VIOLATIONS

---

## ✅ Passed Checks

- Semantic HTML structure uses landmarks (nav, main, footer)
- Single h1 element present
- All images have alt attributes
- Form labels properly associated

---

## ❌ Failed Checks

### 1. Missing ARIA label on hamburger menu button
**Severity**: Critical (WCAG 4.1.2 - Name, Role, Value)
**Location**: index.html:23
**Current**:
```html
<button class="menu-toggle">
  <span class="hamburger-icon"></span>
</button>
```
**Fix**:
```html
<button class="menu-toggle" aria-label="Toggle navigation menu">
  <span class="hamburger-icon"></span>
</button>
```

### 2. Insufficient color contrast on CTA button
**Severity**: Critical (WCAG 1.4.3 - Contrast Minimum)
**Location**: styles/main.css:234
**Current**: White text (#FFFFFF) on light blue (#6CB4EE) = 2.1:1
**Required**: Minimum 4.5:1
**Fix**: Use darker blue (#0056B3) or change text to dark gray (#333333)

### 3. Heading hierarchy skips level
**Severity**: High (WCAG 1.3.1 - Info and Relationships)
**Location**: index.html:67
**Issue**: Jumps from h2 to h4 (skips h3)
**Fix**: Change h4 to h3 or add intermediate h3 section

---

## ⚠️ Manual Testing Required

The following checks cannot be fully automated and require manual browser testing:

- [ ] **Keyboard navigation**: Tab through all interactive elements
- [ ] **Focus indicators**: Verify visible focus states (not removed with outline: none)
- [ ] **Screen reader**: Test with VoiceOver (Mac) or NVDA (Windows)
- [ ] **Zoom test**: Verify layout works at 200% zoom (WCAG 1.4.4)
- [ ] **Color reliance**: Ensure color isn't the only visual indicator

---

## Summary

**Critical Issues**: 2
**High Priority**: 1
**Medium Priority**: 0
**Low Priority**: 0

**WCAG AA Compliance**: ❌ NOT COMPLIANT (critical issues must be resolved)

**Next Steps**:
1. Add aria-label to hamburger menu button
2. Fix color contrast on CTA button
3. Correct heading hierarchy
4. Perform manual keyboard navigation test
5. Re-run accessibility check
```

---

## Severity Levels

| Level | Definition | Examples |
|-------|------------|----------|
| **Critical** | Blocks users with disabilities; violates WCAG A or AA | Missing alt text, insufficient contrast, unlabeled form fields |
| **High** | Significant barrier but workarounds exist | Heading hierarchy issues, missing landmarks, poor focus indicators |
| **Medium** | Usability issue but doesn't block access | Redundant ARIA, suboptimal alt text, minor keyboard nav issues |
| **Low** | Best practice recommendation | Missing skip link, decorative images with descriptive alt |

---

## Workflow

1. **Read index.html** and any other HTML files
2. **Run automated grep checks** for common patterns
3. **Analyze results** against WCAG 2.1 AA guidelines
4. **Document findings** with file paths and line numbers
5. **Provide specific fix recommendations** (code snippets when possible)
6. **Remind about manual testing** requirements
7. **Generate pass/fail report**

---

## WCAG 2.1 AA Quick Reference

### Level A (Must Have)
- 1.1.1 Non-text Content (alt text)
- 1.3.1 Info and Relationships (semantic HTML, heading hierarchy)
- 2.1.1 Keyboard (keyboard accessible)
- 4.1.2 Name, Role, Value (ARIA labels)

### Level AA (Should Have)
- 1.4.3 Contrast (Minimum) - 4.5:1 for normal text, 3:1 for large text
- 1.4.5 Images of Text (avoid text in images)
- 2.4.6 Headings and Labels (descriptive)
- 3.3.3 Error Suggestion (form error guidance)

---

## Tools & Resources

**Automated Testing** (use via code inspection):
- Grep patterns for missing attributes
- CSS analysis for color extraction
- HTML structure validation

**Manual Testing** (user must perform):
- [WebAIM Contrast Checker](https://webaim.org/resources/contrastchecker/)
- Browser DevTools (Lighthouse, Accessibility tab)
- Screen readers (VoiceOver, NVDA, JAWS)
- Keyboard navigation (Tab, Enter, Space, Escape)

**Reference**:
- [WCAG 2.1 Quick Reference](https://www.w3.org/WAI/WCAG21/quickref/)
- [WebAIM Checklist](https://webaim.org/standards/wcag/checklist)

---

## Examples

### Example 1: Icon Button Missing Label

❌ **Bad**:
```html
<button class="search-button">
  <svg>...</svg>
</button>
```

✅ **Good**:
```html
<button class="search-button" aria-label="Search">
  <svg aria-hidden="true">...</svg>
</button>
```

### Example 2: Input Without Label

❌ **Bad**:
```html
<input type="email" placeholder="Enter your email">
```

✅ **Good**:
```html
<label for="email">Email Address</label>
<input type="email" id="email" placeholder="you@example.com">
```

### Example 3: Decorative Image

❌ **Bad**:
```html
<img src="decorative-pattern.png" alt="Decorative pattern">
```

✅ **Good**:
```html
<img src="decorative-pattern.png" alt="">
```

---

## Remember

- **Be specific**: Always include file paths and line numbers
- **Provide fixes**: Give exact code snippets when possible
- **Cite WCAG**: Reference specific guidelines (e.g., 1.4.3)
- **Flag manual tests**: Remind about checks that require browser testing
- **Prioritize**: Use severity levels to guide fix order

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ai-enhanced-engineer) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
