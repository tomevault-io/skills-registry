---
name: accessibility-auditor
description: Audits e-learning content for WCAG 2.1 AA accessibility compliance. Use when checking accessibility, auditing content, reviewing for compliance, or when user mentions "accessibility check," "WCAG audit," "a11y review," "accessibility compliance," or "screen reader. Use when this capability is needed.
metadata:
  author: webmasterarbez
---

# Accessibility Auditor

Guide for auditing e-learning content against WCAG 2.1 AA standards.

## Quick Audit Checklist

### Critical Issues (Must Fix)

- [ ] **Images have alt text** - All informative images have descriptive alt text
- [ ] **Videos have captions** - All video content has synchronized captions
- [ ] **Keyboard accessible** - All interactive elements work with keyboard only
- [ ] **Color contrast** - Text meets 4.5:1 ratio (3:1 for large text)
- [ ] **Focus visible** - Keyboard focus indicator is clearly visible
- [ ] **No auto-play** - Audio/video doesn't play automatically

### High Priority (Should Fix)

- [ ] **Headings structured** - Proper heading hierarchy (H1→H2→H3)
- [ ] **Links descriptive** - Link text describes destination
- [ ] **Forms labeled** - All form fields have visible labels
- [ ] **Error messages clear** - Errors identify field and suggest fix
- [ ] **Audio has transcript** - All audio-only content has text alternative
- [ ] **No flashing** - Content doesn't flash more than 3 times/second

### Best Practices (Recommended)

- [ ] **Reading level** - Content at 8th grade level where possible
- [ ] **Consistent navigation** - Same elements in same location
- [ ] **Timeout warnings** - Users warned before session timeout
- [ ] **Skip links** - Skip to main content link provided
- [ ] **Language set** - Page language declared in HTML

## Audit Report Template

```markdown
# Accessibility Audit Report

**Course:** [Course Name]
**Date:** [Audit Date]
**Auditor:** [Name]
**Standard:** WCAG 2.1 AA

## Executive Summary

**Overall Status:** [Pass / Fail / Conditional Pass]
**Critical Issues:** [Count]
**High Priority Issues:** [Count]
**Recommendations:** [Count]

## Findings by Category

### 1. Perceivable

#### 1.1 Text Alternatives
| Issue | Location | Severity | Recommendation |
|-------|----------|----------|----------------|
| [Issue] | [Page/Screen] | Critical/High/Medium | [Fix] |

#### 1.2 Time-based Media
| Issue | Location | Severity | Recommendation |
|-------|----------|----------|----------------|

#### 1.3 Adaptable
| Issue | Location | Severity | Recommendation |
|-------|----------|----------|----------------|

#### 1.4 Distinguishable
| Issue | Location | Severity | Recommendation |
|-------|----------|----------|----------------|

### 2. Operable

#### 2.1 Keyboard Accessible
| Issue | Location | Severity | Recommendation |
|-------|----------|----------|----------------|

#### 2.4 Navigable
| Issue | Location | Severity | Recommendation |
|-------|----------|----------|----------------|

### 3. Understandable

#### 3.1 Readable
| Issue | Location | Severity | Recommendation |
|-------|----------|----------|----------------|

#### 3.3 Input Assistance
| Issue | Location | Severity | Recommendation |
|-------|----------|----------|----------------|

### 4. Robust

#### 4.1 Compatible
| Issue | Location | Severity | Recommendation |
|-------|----------|----------|----------------|

## Remediation Priority

### Critical (Fix Immediately)
1. [Issue and location]
2. [Issue and location]

### High (Fix Before Launch)
1. [Issue and location]
2. [Issue and location]

### Medium (Fix in Next Update)
1. [Issue and location]
2. [Issue and location]

## Sign-off

| Role | Name | Date |
|------|------|------|
| Auditor | | |
| Remediation Lead | | |
| Approver | | |
```

## Common Issues and Fixes

### Images

**Issue:** Missing alt text
```html
<!-- Bad -->
<img src="diagram.png">

<!-- Good -->
<img src="diagram.png" alt="Sales process flowchart showing 5 steps from lead to close">
```

**Issue:** Decorative image not marked
```html
<!-- Bad -->
<img src="decorative-line.png" alt="decorative line">

<!-- Good -->
<img src="decorative-line.png" alt="" role="presentation">
```

### Color Contrast

**Issue:** Low contrast text
```css
/* Bad - 2.5:1 ratio */
color: #999999;
background-color: #ffffff;

/* Good - 4.5:1 ratio */
color: #595959;
background-color: #ffffff;
```

**Issue:** Color-only indication
```html
<!-- Bad -->
<p style="color: red;">Error in field above</p>

<!-- Good -->
<p style="color: red;"><span aria-hidden="true">⚠</span> Error: Please enter a valid email</p>
```

### Keyboard Navigation

**Issue:** Mouse-only interaction
```html
<!-- Bad -->
<div onclick="showMore()">Show more</div>

<!-- Good -->
<button onclick="showMore()">Show more</button>

<!-- Or if div is required -->
<div role="button" tabindex="0" onclick="showMore()" onkeypress="if(event.key==='Enter')showMore()">
  Show more
</div>
```

**Issue:** Hidden focus indicator
```css
/* Bad */
:focus { outline: none; }

/* Good */
:focus {
  outline: 3px solid #005fcc;
  outline-offset: 2px;
}
```

### Forms

**Issue:** Missing labels
```html
<!-- Bad -->
<input type="text" placeholder="Email">

<!-- Good -->
<label for="email">Email address</label>
<input type="email" id="email" placeholder="name@example.com">
```

**Issue:** Error not associated with field
```html
<!-- Bad -->
<input type="email" id="email">
<span style="color:red">Invalid email</span>

<!-- Good -->
<input type="email" id="email" aria-describedby="email-error" aria-invalid="true">
<span id="email-error" role="alert">Please enter a valid email address</span>
```

### Headings

**Issue:** Skipped heading levels
```html
<!-- Bad -->
<h1>Course Title</h1>
<h3>First Topic</h3>  <!-- Skipped H2 -->

<!-- Good -->
<h1>Course Title</h1>
<h2>Module 1</h2>
<h3>First Topic</h3>
```

### Video/Audio

**Issue:** Missing captions
```html
<!-- Bad -->
<video src="training.mp4" controls></video>

<!-- Good -->
<video src="training.mp4" controls>
  <track kind="captions" src="training-captions.vtt" srclang="en" label="English" default>
</video>
```

## Testing Tools

### Automated Testing

| Tool | Purpose | URL |
|------|---------|-----|
| WAVE | Browser extension for quick checks | wave.webaim.org |
| axe DevTools | Detailed accessibility testing | deque.com/axe |
| Lighthouse | Chrome DevTools audit | Built into Chrome |
| Color Contrast Analyzer | Color testing | paciellogroup.com |

### Manual Testing

| Test | How |
|------|-----|
| Keyboard only | Tab through entire course without mouse |
| Screen reader | Test with NVDA (Windows) or VoiceOver (Mac) |
| Zoom 200% | Verify content reflows, nothing hidden |
| Color blindness | Use simulator extension |
| Captions | Verify accuracy and timing |

### Screen Reader Testing Commands

**NVDA (Windows):**
- `Insert + Down Arrow` - Read all
- `H` - Next heading
- `Tab` - Next focusable element
- `Insert + F7` - Elements list

**VoiceOver (Mac):**
- `VO + A` - Read all
- `VO + Command + H` - Next heading
- `Tab` - Next focusable element
- `VO + U` - Rotor (elements list)

## Severity Definitions

| Severity | Definition | Action |
|----------|------------|--------|
| **Critical** | Prevents access to content for users with disabilities | Must fix before launch |
| **High** | Significant barrier to access | Should fix before launch |
| **Medium** | Causes difficulty but workaround exists | Fix in next update |
| **Low** | Minor inconvenience | Fix when possible |

## WCAG Success Criteria Reference

### Level A (Minimum)
- 1.1.1 Non-text Content
- 1.2.1 Audio-only and Video-only
- 1.3.1 Info and Relationships
- 1.4.1 Use of Color
- 2.1.1 Keyboard
- 2.4.1 Bypass Blocks
- 3.1.1 Language of Page
- 4.1.1 Parsing

### Level AA (Target)
- 1.2.4 Captions (Live)
- 1.2.5 Audio Description
- 1.4.3 Contrast (Minimum)
- 1.4.4 Resize Text
- 2.4.5 Multiple Ways
- 2.4.6 Headings and Labels
- 3.2.3 Consistent Navigation
- 3.2.4 Consistent Identification

## File Output

Save reports to: `course-template/04-evaluation/reports/`

Naming: `accessibility-audit-[date].md`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/webmasterarbez) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
