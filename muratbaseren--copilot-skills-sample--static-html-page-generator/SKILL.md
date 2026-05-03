---
name: static-html-page-generator
description: Create responsive, production-grade static HTML pages using Bootstrap 5 (latest) with modern UI components, icons, dark/light mode, and professional design patterns. Use when creating demo pages, dashboards, sample applications, or UI mockups that need polished, responsive interfaces. Use when this capability is needed.
metadata:
  author: muratbaseren
---

This skill guides creation of responsive HTML pages that leverage Bootstrap 5 to produce professional, fully-functional static web applications. The approach emphasizes clean code, semantic HTML, proper Bootstrap patterns, and modern UI/UX features.

> **Note**: For data management features (search, sort, filter, pagination) and Faker.js integration, see [data-features-with-faker](../data-features-with-faker/SKILL.md). For SEO optimization, see [seo-optimization](../seo-optimization/SKILL.md).

## Before Coding: Requirements & Planning

When a user requests an HTML page, clarify these points:

- **Page Type & Purpose**: Dashboard, data table, product listing, profile page, admin panel, landing page, form showcase, etc.
- **Layout Structure**: Single column, multi-column grid, sidebar layout, card-based, table-based?
- **Interactivity Level**: Static content only, collapsible sections, modals, tabs?
- **Responsive Breakpoints**: Mobile-first for all devices? Desktop-focused with mobile support?
- **Styling Preference**: Minimalist/clean, colorful/vibrant, dark mode, custom brand colors?

Once requirements are clear, provide complete, working HTML file.

## Technical Foundation

### Bootstrap 5 & Icon Setup

Always include Bootstrap 5 latest from CDN:

```html
<!-- Bootstrap 5 CSS -->
<link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css" rel="stylesheet">

<!-- Font Awesome 6 Icons -->
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">

<!-- Bootstrap 5 JS Bundle (includes Popper.js) -->
<script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/js/bootstrap.bundle.min.js"></script>
```

### Font Awesome Icon Usage

**Icon Search:** Use [fontawesome.com](https://fontawesome.com/icons) to find icons. Prefix with `fas` for solid, `far` for regular, `fab` for brands.

**Common Icons:**
- Buttons: `fa-save`, `fa-trash`, `fa-edit`, `fa-check`, `fa-times`, `fa-plus`
- Navigation: `fa-home`, `fa-user`, `fa-cog`, `fa-search`, `fa-bars`
- Data: `fa-table`, `fa-list`, `fa-chart-bar`, `fa-database`
- Status: `fa-circle`, `fa-check-circle`, `fa-times-circle`, `fa-exclamation-triangle`
- UI: `fa-chevron-left`, `fa-chevron-right`, `fa-chevron-down`, `fa-spinner`

**Usage Examples:**
```html
<!-- In buttons -->
<button class="btn btn-primary">
    <i class="fas fa-save me-1"></i>Save
</button>

<!-- Icon-only button -->
<button class="btn btn-sm btn-danger" title="Delete" aria-label="Delete">
    <i class="fas fa-trash"></i>
</button>

<!-- In text -->
<span class="me-2"><i class="fas fa-envelope"></i></span> Email
```

### Bootstrap 5 Key Concepts

- **Grid System**: `.container`, `.row`, `.col-*` (12-column grid, breakpoints: xs, sm, md, lg, xl, xxl)
- **Components**: Navbar, cards, tables, buttons, badges, alerts, modals, forms, dropdowns
- **Utilities**: Spacing (m/p), text alignment, colors, display, flexbox (d-flex, justify-content, align-items)
- **Responsive Classes**: `d-none d-md-block` for showing/hiding at breakpoints

## HTML Structure Best Practices

### Semantic Markup
- Use `<main>`, `<section>`, `<article>`, `<header>`, `<footer>` for structure
- Use `<table>` for tabular data (not divs)
- Use `<form>` with proper `<label>` associations
- Use semantic HTML5 elements, not generic divs

### Bootstrap Grid Rules
```html
<!-- Correct structure -->
<div class="container">
  <div class="row">
    <div class="col-md-6">Content</div>
    <div class="col-md-6">Content</div>
  </div>
</div>
```

**Key points:**
- Use `.container` or `.container-fluid` for max-width wrapping
- `.row` must be direct child of `.container`
- Columns must be direct children of `.row`
- Content goes inside columns

### Responsive Images
```html
<!-- Bootstrap responsive image with lazy loading -->
<img src="path/to/image.jpg" 
     class="img-fluid" 
     alt="Description"
     loading="lazy"
     decoding="async">
```

## Essential Page Components

### Search Bar (Top-Right)
Always include a search input in the navbar or header for professional appearance.

### Status Bar (Footer)
Include at page bottom for system status and metadata display.

### Page Navigation
For pages displaying lists or data, include appropriate navigation controls.

> **Note**: For detailed implementation of search, pagination, sorting, and filtering, see [data-features-with-faker](../data-features-with-faker/SKILL.md).

## Dark Mode & Light Mode Implementation

Bootstrap 5 supports dark mode through CSS data attributes.

### Setup
```html
<!-- Add data-bs-theme attribute to <html> root element -->
<html lang="en" data-bs-theme="light">
```

### Toggle Button
```html
<!-- Add to navbar or header -->
<button class="btn btn-sm" id="themeToggle" aria-label="Toggle dark mode">
    <i class="fas fa-moon"></i>
</button>
```

### JavaScript Theme Logic
```javascript
// Initialize theme on page load
function initTheme() {
    const saved = localStorage.getItem('theme') || 'light';
    document.documentElement.setAttribute('data-bs-theme', saved);
    updateThemeIcon(saved);
}

// Toggle between light and dark
function toggleTheme() {
    const current = document.documentElement.getAttribute('data-bs-theme');
    const newTheme = current === 'light' ? 'dark' : 'light';
    
    document.documentElement.setAttribute('data-bs-theme', newTheme);
    localStorage.setItem('theme', newTheme);
    updateThemeIcon(newTheme);
}

// Update icon based on theme
function updateThemeIcon(theme) {
    const icon = document.querySelector('#themeToggle i');
    if (theme === 'dark') {
        icon.classList.remove('fa-moon');
        icon.classList.add('fa-sun');
    } else {
        icon.classList.remove('fa-sun');
        icon.classList.add('fa-moon');
    }
}

// Initialize on page load
document.addEventListener('DOMContentLoaded', initTheme);
document.getElementById('themeToggle').addEventListener('click', toggleTheme);
```

### CSS Variables for Custom Colors
```css
<style>
    :root[data-bs-theme="light"] {
        --custom-bg: #ffffff;
        --custom-text: #000000;
        --custom-border: #e0e0e0;
    }
    
    :root[data-bs-theme="dark"] {
        --custom-bg: #1a1a1a;
        --custom-text: #ffffff;
        --custom-border: #333333;
    }
    
    .custom-element {
        background-color: var(--custom-bg);
        color: var(--custom-text);
        border: 1px solid var(--custom-border);
    }
</style>
```

**Key Points:**
- Light mode is default
- Theme preference is saved to `localStorage`
- Dark mode automatically inverts Bootstrap component colors
- Icon changes to reflect current theme (moon = light mode, sun = dark mode)

## Button Interaction & Locking Mechanism

All buttons lock for 3 seconds after click to prevent accidental double-submission.

### JavaScript Implementation

```javascript
// Attach button lock handlers to all buttons
function attachButtonLockHandlers() {
    const buttons = document.querySelectorAll('button:not([data-no-lock])');
    
    buttons.forEach(button => {
        button.addEventListener('click', handleButtonClick);
    });
}

function handleButtonClick(event) {
    const button = event.currentTarget;
    
    // Skip if already disabled
    if (button.disabled) {
        event.preventDefault();
        return;
    }
    
    // Store original content
    const originalHTML = button.innerHTML;
    
    // Lock button
    button.disabled = true;
    button.innerHTML = '<i class="fas fa-spinner fa-spin me-2"></i>Processing...';
    button.classList.add('opacity-75');
    
    // Unlock after 3 seconds
    setTimeout(() => {
        button.disabled = false;
        button.innerHTML = originalHTML;
        button.classList.remove('opacity-75');
    }, 3000);
}

// Initialize on page load
document.addEventListener('DOMContentLoaded', () => {
    attachButtonLockHandlers();
});
```

### HTML Button Examples

```html
<!-- Standard button - will auto-lock -->
<button class="btn btn-primary">
    <i class="fas fa-save me-1"></i>Save
</button>

<!-- Button that should NOT lock -->
<button class="btn btn-secondary" data-no-lock>
    <i class="fas fa-times"></i>Cancel
</button>
```

**Important:**
- Lock duration is 3 seconds (3000ms)
- All buttons locked by default; exclude with `data-no-lock` attribute
- Button becomes semi-transparent during processing
- Original HTML (including icons) is preserved

## Responsive Design Checklist

- ✅ Meta viewport tag: `<meta name="viewport" content="width=device-width, initial-scale=1.0">`
- ✅ Container wrapper: `.container` or `.container-fluid`
- ✅ Proper column classes: `.col-md-6`, `.col-lg-4`, etc.
- ✅ Bootstrap utilities: spacing (p-*, m-*), alignment, colors
- ✅ Responsive images: `.img-fluid` class with lazy loading
- ✅ Collapsible navbar: `.navbar-expand-lg`, `.navbar-toggler`
- ✅ Table wrapper: `.table-responsive` for mobile
- ✅ Touch-friendly elements: adequate sizes for mobile
- ✅ Search bar: top-right position, responsive width
- ✅ Status bar: fixed at bottom
- ✅ Button lock mechanism: 3-second processing state
- ✅ Dark/Light mode toggle with persistence

## Code Quality Guidelines

### JavaScript Organization
- Use meaningful variable names
- Comment complex logic
- Extract reusable functions
- Initialize on DOMContentLoaded

### CSS & Styling
- Leverage Bootstrap utilities first
- Use CSS variables for theme colors
- Minimal custom CSS in `<style>` tag
- Avoid inline styles

### Icon Best Practices
- Add `aria-label` to icon-only buttons
- Use consistent icon sizes (`.fa-lg`, `.fa-xl`)
- Pair icons with text when needed
- Use semantic icon choices

### HTML Cleanliness
- Proper indentation (2 spaces)
- Semantic HTML5 elements
- Meaningful alt text on images
- Always include `loading="lazy"` on images

### Dark Mode Implementation
- Initialize theme on page load
- Save preference to localStorage
- Test all colors in both modes
- Use Bootstrap semantic color classes

## Common Pitfalls & Solutions

| Problem | Solution |
|---------|----------|
| Inconsistent column widths | Use `.col-12` for xs, then `.col-md-6`, `.col-lg-4` for larger screens |
| Layout breaks on large screens | Use `.container` (max-width) instead of `.container-fluid` |
| Text too small on mobile | Use responsive font sizes, add mobile padding |
| Images stretched | Always use `.img-fluid` + lazy loading |
| Gaps between cards missing | Use `g-*` classes: `<div class="row g-4">` |
| Icons look tiny | Use `.fa-lg`, `.fa-xl`, `.fa-2x` classes |
| Dark mode not working | Ensure `data-bs-theme` on `<html>` root |
| Theme not persisting | Use `localStorage.setItem('theme', newTheme)` |
| Button lock not working | Attach listener after DOM ready |
| Status bar not at bottom | Use `d-flex flex-column` on body with `min-height: 100vh` |

## Deliverable Expectations

When providing code:

1. ✅ **Complete, working HTML file** - Opens directly in browser
2. ✅ **Proper DOCTYPE & meta tags** - Valid HTML5
3. ✅ **Bootstrap 5 latest CDN** - From jsdelivr
4. ✅ **Font Awesome 6.4.0+ CDN** - Icon support
5. ✅ **Dark/Light mode toggle** - With localStorage persistence
6. ✅ **Search bar** - Top-right in navbar
7. ✅ **Status bar footer** - Bottom of page
8. ✅ **Button lock mechanism** - 3-second processing state
9. ✅ **Lazy loading images** - `loading="lazy"` and `decoding="async"`
10. ✅ **Icon usage** - In buttons, headers, badges
11. ✅ **Responsive design** - Mobile (320px+), tablet, desktop
12. ✅ **Clean, readable code** - Proper indentation, comments
13. ✅ **No dependencies** - Works with CDN links only

---

**Remember**: Focus on clean, semantic HTML, proper Bootstrap patterns, and modern UI/UX features. For data features, reference [data-features-with-faker](../data-features-with-faker/SKILL.md). For SEO, reference [seo-optimization](../seo-optimization/SKILL.md).

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/muratbaseren) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
