---
name: visual-test
description: Visual and responsive design validation across mobile, tablet, and desktop viewports. Checks layout consistency, typography, navigation behavior, tap targets, and image responsiveness. Use when user says "visual test", "responsive", "check mobile", or "cross-browser". Use when this capability is needed.
metadata:
  author: ai-enhanced-engineer
---

# Visual Test Skill

## Purpose

Validate visual and responsive design across multiple device viewports using code inspection and optional browser automation via Playwright.

---

## When to Use

**Trigger phrases**:
- "visual test"
- "check responsive design"
- "test mobile layout"
- "cross-browser check"
- "validate responsive"
- "check different screen sizes"

**Use cases**:
- Pre-deployment responsive design validation
- After adding new sections or components
- Troubleshooting layout issues on specific devices
- Client deliverable quality check

---

## Responsive Breakpoints

### Mobile Viewports
- **320px** - iPhone SE, small phones
- **375px** - iPhone 12/13, standard phones
- **414px** - iPhone 12/13 Pro Max, large phones

### Tablet Viewports
- **768px** - iPad portrait, small tablets
- **1024px** - iPad landscape, large tablets

### Desktop Viewports
- **1280px** - Laptop screens, small desktops
- **1920px** - Full HD displays, large desktops

---

## Testing Approaches

### Approach 1: Code Inspection (Always Available)

**Check CSS for**:
- Media queries covering key breakpoints
- Responsive units (%, rem, em, vw, vh)
- Flexbox/Grid for flexible layouts
- Max-width containers
- Mobile-first or desktop-first approach

**Validation**:
```bash
# Find media queries
grep -n '@media' styles/*.css

# Check for responsive units
grep -n 'rem\|em\|%\|vw\|vh' styles/*.css

# Find fixed widths (potential issues)
grep -n 'width:.*px' styles/*.css
```

**Analysis**:
- Are breakpoints defined for 768px, 1024px, 1280px?
- Does mobile layout use single column (stack vertically)?
- Are images responsive (max-width: 100% or srcset)?
- Is typography scalable (rem/em vs px)?

---

### Approach 2: Browser Automation (Playwright MCP)

**If Playwright is available**, automate visual testing:

**Workflow**:
1. Navigate to page (or local server)
2. Resize viewport to each breakpoint
3. Take screenshots
4. Analyze layout visually
5. Check for horizontal scroll
6. Test interactive elements (navigation, buttons)

**Example Playwright sequence**:
```javascript
// Pseudocode - actual implementation via mcp__playwright__ tools
1. browser_navigate to http://localhost:5174
2. browser_resize to 320×568 (iPhone SE)
3. browser_take_screenshot filename="mobile-320.png"
4. browser_resize to 768×1024 (iPad portrait)
5. browser_take_screenshot filename="tablet-768.png"
6. browser_resize to 1920×1080 (desktop)
7. browser_take_screenshot filename="desktop-1920.png"
8. browser_snapshot (accessibility tree for layout verification)
```

---

## Visual Checklist

### 1. Mobile Layout (320px - 414px)

**Navigation**:
- [ ] Hamburger menu visible or navigation stacks vertically
- [ ] Menu toggle button ≥44×44px tap target
- [ ] Dropdown/mobile menu expands correctly
- [ ] Logo/brand visible and appropriately sized

**Typography**:
- [ ] Body text ≥16px (prevents auto-zoom on iOS)
- [ ] Line height 1.5-1.8 for readability
- [ ] Headings scale appropriately (h1 not too large)
- [ ] Line length <75 characters (readability)

**Layout**:
- [ ] Single-column layout (vertical stacking)
- [ ] No horizontal scrolling
- [ ] Adequate spacing between sections (min 20px)
- [ ] Content fits within viewport (no overflow)

**Images**:
- [ ] Scale to container width (max-width: 100%)
- [ ] Maintain aspect ratio (no distortion)
- [ ] Lazy loading for below-fold images
- [ ] Srcset for different resolutions (optional but recommended)

**Buttons & Links**:
- [ ] Tap targets ≥44×44px (iOS accessibility guideline)
- [ ] Adequate spacing between interactive elements (min 8px)
- [ ] Buttons are full-width or appropriately sized
- [ ] CTA buttons visible without scrolling (above fold)

**Forms**:
- [ ] Inputs stack vertically
- [ ] Input fields ≥44px height
- [ ] Labels visible above inputs
- [ ] Submit button full-width or prominent

---

### 2. Tablet Layout (768px - 1024px)

**Navigation**:
- [ ] Transitions smoothly from mobile to desktop style
- [ ] Horizontal navigation (if applicable) or visible menu
- [ ] Logo + nav items fit within width

**Layout**:
- [ ] Two-column layout (if applicable) or wider single column
- [ ] Content uses available space (not too narrow)
- [ ] Grid/flex layouts adjust appropriately
- [ ] Sidebar visible (if applicable)

**Images**:
- [ ] Higher resolution images load (srcset)
- [ ] Gallery/grid layouts use 2-3 columns
- [ ] Hero images scale appropriately

**Typography**:
- [ ] Font sizes increase slightly from mobile
- [ ] Line length remains readable (60-75 chars)
- [ ] Adequate whitespace

---

### 3. Desktop Layout (1280px - 1920px)

**Navigation**:
- [ ] Full horizontal navigation visible
- [ ] Logo + nav items + CTA fit within max-width container
- [ ] Hover states work (desktop only)

**Layout**:
- [ ] Max-width container (typically 1200-1400px) prevents excessive line length
- [ ] Layout centered or uses grid effectively
- [ ] Multi-column layouts (3-4 columns for grids)
- [ ] No wasted space (content fills width appropriately)

**Images**:
- [ ] High-resolution images (2x or vector)
- [ ] Background images cover/contain appropriately
- [ ] No pixelation or blur

**Typography**:
- [ ] Optimal font sizes (16-18px body, 32-48px+ headings)
- [ ] Line length ≤75 characters (may need narrower container)
- [ ] Generous whitespace

**Interactive Elements**:
- [ ] Hover effects on links/buttons
- [ ] Cursor changes to pointer on clickable elements
- [ ] Focus states visible (keyboard navigation)

---

## Code Inspection Validation

### CSS Media Queries

**Look for**:
```css
/* Mobile-first approach (recommended) */
/* Base styles: mobile (320px+) */

@media (min-width: 768px) {
  /* Tablet styles */
}

@media (min-width: 1024px) {
  /* Desktop styles */
}

@media (min-width: 1280px) {
  /* Large desktop styles */
}
```

**Or desktop-first approach**:
```css
/* Base styles: desktop */

@media (max-width: 1024px) {
  /* Tablet styles */
}

@media (max-width: 768px) {
  /* Mobile styles */
}
```

**Check**:
- [ ] Breakpoints align with common devices
- [ ] Mobile and desktop layouts have distinct styles
- [ ] No overlapping or conflicting media queries

---

### Responsive Units

**Good**:
```css
.container {
  max-width: 1200px; /* Fixed max-width OK */
  width: 100%; /* Flexible width */
  padding: 2rem; /* Scalable spacing */
  font-size: 1.125rem; /* Scalable typography */
}
```

**Bad**:
```css
.container {
  width: 960px; /* Fixed width - not responsive */
  padding: 32px; /* Fixed spacing - doesn't scale */
  font-size: 18px; /* Fixed typography - doesn't scale */
}
```

**Validation**:
- [ ] Width/height use %, rem, em, vw, vh (not always px)
- [ ] Padding/margin use rem/em (scalable)
- [ ] Typography uses rem/em (accessible, scalable)

---

### Flexbox/Grid Layouts

**Flexbox**:
```css
.navigation {
  display: flex;
  flex-wrap: wrap; /* Allow wrapping on small screens */
  justify-content: space-between;
}
```

**Grid**:
```css
.gallery {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(250px, 1fr)); /* Responsive grid */
  gap: 1rem;
}
```

**Check**:
- [ ] Flex/grid containers allow content to reflow
- [ ] flex-wrap: wrap or grid auto-fit/auto-fill for responsiveness
- [ ] Gap spacing defined

---

### Image Responsiveness

**HTML**:
```html
<!-- Responsive image (CSS-based) -->
<img src="image.jpg" alt="Description" style="max-width: 100%; height: auto;">

<!-- Responsive image (srcset) -->
<img src="image-800.jpg"
     srcset="image-400.jpg 400w, image-800.jpg 800w, image-1200.jpg 1200w"
     sizes="(max-width: 768px) 100vw, 50vw"
     alt="Description">
```

**CSS**:
```css
img {
  max-width: 100%;
  height: auto;
}
```

**Check**:
- [ ] Images have max-width: 100% or width: 100%
- [ ] height: auto maintains aspect ratio
- [ ] Srcset provides multiple resolutions (optional)

---

## Output Format

```markdown
# Visual & Responsive Design Test Report

**Date**: [current date]
**Testing Method**: Code Inspection / Browser Automation (Playwright)
**Viewports Tested**: Mobile (320-414px), Tablet (768-1024px), Desktop (1280-1920px)
**Overall Status**: ✅ PASS / ⚠️ ISSUES FOUND / ❌ CRITICAL ISSUES

---

## Mobile Layout (320px - 414px)

### ✅ Passed Checks
- Navigation uses hamburger menu
- Body text is 16px (no auto-zoom)
- Single-column layout
- Images scale responsively (max-width: 100%)

### ❌ Issues Found

**1. Tap targets too small**
**Severity**: High (Accessibility & UX)
**Location**: styles/main.css:123 (.menu-toggle)
**Current**: Button is 32×32px
**Required**: Minimum 44×44px (iOS guideline)
**Recommendation**:
```css
.menu-toggle {
  min-width: 44px;
  min-height: 44px;
  padding: 0.5rem;
}
```

**2. Horizontal scroll on small screens**
**Severity**: Critical
**Location**: styles/main.css:67 (.hero-section)
**Current**: Fixed width: 1200px
**Issue**: Exceeds viewport on mobile (320-414px)
**Recommendation**:
```css
.hero-section {
  max-width: 1200px; /* Use max-width instead */
  width: 100%;
}
```

**Screenshot**: mobile-320.png (attached)

---

## Tablet Layout (768px - 1024px)

### ✅ Passed Checks
- Navigation transitions to horizontal menu
- Two-column layout for content sections
- Images scale appropriately

### ⚠️ Issues Found

**1. Navigation menu overlaps logo at 768px**
**Severity**: Medium
**Location**: styles/main.css:234 (@media (min-width: 768px))
**Issue**: Logo and nav items don't fit within viewport
**Recommendation**: Adjust breakpoint to 800px or reduce nav item spacing
```css
@media (min-width: 800px) { /* Or adjust spacing */
  .navigation {
    gap: 1rem; /* Reduce from 2rem */
  }
}
```

**Screenshot**: tablet-768.png (attached)

---

## Desktop Layout (1280px - 1920px)

### ✅ Passed Checks
- Full horizontal navigation visible
- Max-width container (1200px) prevents excessive line length
- High-resolution images
- Hover states work

### ✅ No Issues Found
Desktop layout is well-optimized.

**Screenshot**: desktop-1920.png (attached)

---

## CSS Analysis

### Media Queries
**Status**: ✅ PASS
- Mobile-first approach used
- Breakpoints at 768px, 1024px, 1280px
- No conflicting queries

**Example**:
```css
/* Base: mobile */
@media (min-width: 768px) { /* Tablet */ }
@media (min-width: 1024px) { /* Desktop */ }
@media (min-width: 1280px) { /* Large desktop */ }
```

### Responsive Units
**Status**: ⚠️ NEEDS IMPROVEMENT
- Typography uses rem ✅
- Spacing uses rem ✅
- Some fixed pixel widths found ❌ (see issues above)

### Flexbox/Grid
**Status**: ✅ PASS
- Navigation uses flexbox with flex-wrap
- Gallery uses CSS Grid with auto-fit
- Responsive gap spacing

---

## Cross-Browser Compatibility

**Manual Testing Required**:
- [ ] Chrome (desktop + mobile)
- [ ] Firefox (desktop)
- [ ] Safari (desktop + iOS)
- [ ] Edge (Chromium)

**Code Inspection**:
- ✅ Valid HTML5 (no syntax errors found)
- ✅ CSS uses modern properties (flexbox, grid)
- ⚠️ Vendor prefixes: Use autoprefixer for production

---

## Summary

**Critical Issues**: 1
- Horizontal scroll on mobile due to fixed width

**High Priority**: 1
- Tap targets too small (accessibility)

**Medium Priority**: 1
- Navigation overlap at 768px breakpoint

**Low Priority**: 0

**Responsive Design Readiness**: ⚠️ NEEDS FIXES

**Next Steps**:
1. Fix horizontal scroll issue (change width to max-width)
2. Increase tap target size for menu toggle (≥44×44px)
3. Adjust navigation breakpoint or spacing
4. Re-test on actual devices (iPhone, iPad, Android)
5. Run Lighthouse audit for additional insights

**Recommended for Production**: ❌ NO (fix critical issue first)
```

---

## Browser Automation Workflow (Playwright)

**If Playwright MCP is available**, use this workflow:

### Step 1: Start Local Server
```bash
# User should run: just serve
# Verify server is running at http://localhost:5174
```

### Step 2: Navigate to Page
```
mcp__playwright__browser_navigate
url: http://localhost:5174
```

### Step 3: Test Mobile Viewport
```
mcp__playwright__browser_resize
width: 320
height: 568

mcp__playwright__browser_take_screenshot
filename: "visual-test-mobile-320.png"

mcp__playwright__browser_snapshot
# Analyze layout in accessibility tree
```

### Step 4: Test Tablet Viewport
```
mcp__playwright__browser_resize
width: 768
height: 1024

mcp__playwright__browser_take_screenshot
filename: "visual-test-tablet-768.png"
```

### Step 5: Test Desktop Viewport
```
mcp__playwright__browser_resize
width: 1920
height: 1080

mcp__playwright__browser_take_screenshot
filename: "visual-test-desktop-1920.png"
```

### Step 6: Test Interactive Elements
```
mcp__playwright__browser_snapshot
# Get accessibility tree

mcp__playwright__browser_click
element: "Navigation menu toggle"
ref: [from snapshot]

# Verify menu opens

mcp__playwright__browser_take_screenshot
filename: "mobile-menu-open.png"
```

---

## Manual Testing Recommendations

**After code inspection/automation**, recommend:

1. **Real device testing**:
   - iPhone (Safari iOS)
   - Android phone (Chrome)
   - iPad (Safari iPadOS)
   - Desktop browsers (Chrome, Firefox, Safari, Edge)

2. **Lighthouse audit** (Chrome DevTools):
   - Performance score
   - Accessibility score
   - Best practices
   - SEO score

3. **BrowserStack or similar** (cross-browser testing):
   - Test on older browsers (if supporting IE11, etc.)
   - Test on various devices and OS versions

4. **Rotate device** (portrait/landscape):
   - Tablet landscape (1024×768)
   - Phone landscape (667×375)

---

## Common Responsive Issues

| Issue | Symptom | Fix |
|-------|---------|-----|
| Horizontal scroll | Content extends past viewport | Change `width: Xpx` to `max-width: Xpx; width: 100%;` |
| Text too small on mobile | Users must zoom to read | Increase font-size to ≥16px |
| Tap targets too small | Hard to click on mobile | Increase to ≥44×44px |
| Fixed-width images | Images overflow container | Add `max-width: 100%; height: auto;` |
| Navigation doesn't collapse | Desktop nav squished on mobile | Add hamburger menu with media query |
| Layout doesn't reflow | Desktop layout on mobile | Add media queries with mobile styles |
| Images pixelated on desktop | Low-res images scaled up | Use srcset or higher resolution images |

---

## Severity Levels

| Severity | Definition | Example |
|----------|------------|---------|
| **Critical** | Breaks layout or makes content inaccessible | Horizontal scroll, overlapping content, invisible text |
| **High** | Significant UX issue | Small tap targets, tiny text, non-responsive images |
| **Medium** | Noticeable but doesn't break experience | Awkward spacing, suboptimal breakpoints, minor layout shifts |
| **Low** | Nice-to-have improvement | Missing hover states, could optimize breakpoint, minor alignment issues |

---

## Remember

- **Test all breakpoints**: Mobile, tablet, desktop
- **Code inspection first**: Faster than browser automation
- **Use Playwright** if available for visual verification
- **Check both portrait and landscape** (especially tablets)
- **Verify tap target sizes**: Minimum 44×44px
- **Document with screenshots**: Visual evidence helps developers
- **Prioritize**: Fix critical issues (horizontal scroll) before minor ones

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ai-enhanced-engineer) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
