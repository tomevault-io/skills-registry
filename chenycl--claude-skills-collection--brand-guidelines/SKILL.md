---
name: brand-guidelines
description: > Use when this capability is needed.
metadata:
  author: chenycl
---

# Brand Guidelines

Apply professional brand colors and typography to artifacts and documents.

## Core Brand System

### Primary Colors
```css
:root {
  /* Primary */
  --brand-primary: #2563eb;        /* Main brand color */
  --brand-primary-dark: #1d4ed8;   /* Hover/active states */
  --brand-primary-light: #3b82f6;  /* Lighter variant */

  /* Secondary */
  --brand-secondary: #64748b;      /* Supporting color */
  --brand-secondary-dark: #475569;
  --brand-secondary-light: #94a3b8;

  /* Accent */
  --brand-accent: #f59e0b;         /* Call-to-action, highlights */
  --brand-accent-dark: #d97706;
}
```

### Neutral Colors
```css
:root {
  /* Backgrounds */
  --neutral-white: #ffffff;
  --neutral-50: #f8fafc;
  --neutral-100: #f1f5f9;
  --neutral-200: #e2e8f0;

  /* Text */
  --neutral-600: #475569;
  --neutral-700: #334155;
  --neutral-800: #1e293b;
  --neutral-900: #0f172a;
}
```

### Semantic Colors
```css
:root {
  --success: #22c55e;
  --success-light: #dcfce7;
  --warning: #f59e0b;
  --warning-light: #fef3c7;
  --error: #ef4444;
  --error-light: #fee2e2;
  --info: #3b82f6;
  --info-light: #dbeafe;
}
```

## Typography

### Font Stack
```css
:root {
  --font-heading: 'Inter', -apple-system, BlinkMacSystemFont, sans-serif;
  --font-body: 'Inter', -apple-system, BlinkMacSystemFont, sans-serif;
  --font-mono: 'JetBrains Mono', 'Fira Code', monospace;
}
```

### Type Scale
```css
/* Headings */
.h1 { font-size: 2.5rem; font-weight: 700; line-height: 1.2; }
.h2 { font-size: 2rem; font-weight: 600; line-height: 1.25; }
.h3 { font-size: 1.5rem; font-weight: 600; line-height: 1.3; }
.h4 { font-size: 1.25rem; font-weight: 600; line-height: 1.4; }

/* Body */
.body-large { font-size: 1.125rem; line-height: 1.6; }
.body { font-size: 1rem; line-height: 1.6; }
.body-small { font-size: 0.875rem; line-height: 1.5; }
.caption { font-size: 0.75rem; line-height: 1.4; }
```

## Spacing System

```css
/* 4px base unit */
--space-1: 0.25rem;  /* 4px */
--space-2: 0.5rem;   /* 8px */
--space-3: 0.75rem;  /* 12px */
--space-4: 1rem;     /* 16px */
--space-5: 1.25rem;  /* 20px */
--space-6: 1.5rem;   /* 24px */
--space-8: 2rem;     /* 32px */
--space-10: 2.5rem;  /* 40px */
--space-12: 3rem;    /* 48px */
--space-16: 4rem;    /* 64px */
```

## Component Styles

### Buttons
```css
.btn-primary {
  background: var(--brand-primary);
  color: white;
  padding: var(--space-3) var(--space-6);
  border-radius: 0.5rem;
  font-weight: 500;
}

.btn-primary:hover {
  background: var(--brand-primary-dark);
}

.btn-secondary {
  background: transparent;
  color: var(--brand-primary);
  border: 1px solid var(--brand-primary);
  padding: var(--space-3) var(--space-6);
  border-radius: 0.5rem;
}
```

### Cards
```css
.card {
  background: var(--neutral-white);
  border: 1px solid var(--neutral-200);
  border-radius: 0.75rem;
  padding: var(--space-6);
  box-shadow: 0 1px 3px rgba(0, 0, 0, 0.1);
}
```

### Forms
```css
.input {
  border: 1px solid var(--neutral-200);
  border-radius: 0.5rem;
  padding: var(--space-3) var(--space-4);
  font-size: 1rem;
}

.input:focus {
  outline: none;
  border-color: var(--brand-primary);
  box-shadow: 0 0 0 3px var(--brand-primary-light);
}
```

## Document Styles

### Word/PDF Headers
```python
header_style = {
    'font': 'Inter',
    'size': 28,
    'color': '#1e293b',
    'weight': 'bold',
    'margin_bottom': 16
}

body_style = {
    'font': 'Inter',
    'size': 11,
    'color': '#334155',
    'line_height': 1.6
}
```

### Presentation Slides
```python
slide_theme = {
    'title_font': 'Inter',
    'title_size': 44,
    'title_color': '#0f172a',

    'body_font': 'Inter',
    'body_size': 24,
    'body_color': '#334155',

    'accent_color': '#2563eb',
    'background': '#ffffff',
    'slide_number_color': '#64748b'
}
```

## Usage Guidelines

### Do's
- Use primary color for main CTAs and key elements
- Maintain consistent spacing using the scale
- Use semantic colors for status indicators
- Ensure sufficient contrast (4.5:1 for text)

### Don'ts
- Don't use brand colors for error states
- Don't mix multiple accent colors
- Don't use light text on light backgrounds
- Don't deviate from the type scale

### Accessibility
- Minimum contrast ratio: 4.5:1 for normal text
- Minimum contrast ratio: 3:1 for large text
- Don't rely solely on color for information
- Ensure interactive elements are clearly visible

## Color Combinations

### Recommended Pairs
| Background | Text | Accent |
|------------|------|--------|
| White | neutral-800 | primary |
| neutral-50 | neutral-900 | primary |
| primary | white | accent |
| neutral-900 | white | primary-light |

### Avoid
| Background | Text | Issue |
|------------|------|-------|
| primary-light | white | Low contrast |
| neutral-200 | neutral-400 | Insufficient contrast |
| accent | primary | Clashing colors |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/chenycl) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
