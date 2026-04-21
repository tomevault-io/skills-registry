---
name: aaa-ui-ux
description: AAA (Triple-A) UI/UX practices and standards for premium, polished user interfaces that rival Linear, Stripe, and Apple. Use when this capability is needed.
metadata:
  author: atlasrox
---

# 🎮 AAA UI/UX Practices

**Goal**: Deliver game-industry-level quality interfaces that feel premium, responsive, and delightful.

---

## 🌟 What is AAA UI/UX?

AAA (Triple-A) refers to the highest quality standard in software development—interfaces that feel premium, responsive, and delightful. Think: **Linear, Stripe, Apple, Raycast, Vercel, Figma**.

### The AAA Mindset

| Principle | Description |
|-----------|-------------|
| **Pixel Perfection** | Every element is intentionally placed, aligned to the grid |
| **Invisible Design** | Users accomplish goals without noticing the interface |
| **Delightful Details** | Micro-interactions that make users smile |
| **Consistent Experience** | Every component feels part of the same family |
| **Performance as UX** | Fast is a feature—60fps animations, instant feedback |

---

## 📋 The 7 Pillars of AAA UI/UX

### 1. 🏗️ Visual Hierarchy & Layout

#### The 8px Grid System

All spacing should be multiples of 8px for consistent, harmonious layouts:

| Token | Value | Usage |
|-------|-------|-------|
| `space-1` | 4px | Icon padding, tight gaps |
| `space-2` | 8px | Default component gap |
| `space-3` | 12px | List item padding |
| `space-4` | 16px | Card internal padding |
| `space-6` | 24px | Section padding |
| `space-8` | 32px | Large section gaps |
| `space-12` | 48px | Page section separators |
| `space-16` | 64px | Hero section padding |

```css
/* ✅ GOOD: Consistent 8px grid */
.card {
  padding: 24px;        /* 3 × 8 = 24 */
  gap: 16px;            /* 2 × 8 = 16 */
  margin-bottom: 32px;  /* 4 × 8 = 32 */
}

/* ❌ BAD: Random values */
.card {
  padding: 23px;
  gap: 17px;
  margin-bottom: 35px;
}
```

#### Visual Hierarchy Techniques

```
┌─────────────────────────────────────────────────┐
│  ▲ Primary (Largest, Boldest, First Attention) │
│  │                                             │
│  │  Secondary (Supporting, Smaller)            │
│  │    │                                        │
│  │    │  Tertiary (Details, Muted Colors)      │
│  │    │    │                                   │
│  ▼    ▼    ▼  Quaternary (Metadata, Captions)  │
└─────────────────────────────────────────────────┘
```

##### Hierarchy Rules:
1. **Size** – Bigger = More important
2. **Weight** – Bolder = More important
3. **Color** – Darker/Accent = More important
4. **Position** – Top-left = First seen (F-pattern)
5. **Whitespace** – More space = More emphasis

#### Layout Patterns

| Pattern | Use Case | Example |
|---------|----------|---------|
| **F-Pattern** | Content-heavy pages | Blog, news site |
| **Z-Pattern** | Landing pages | Marketing pages |
| **Gutenberg Diagram** | Text-heavy pages | Documentation |
| **Card Grid** | Browse/explore | Dashboard, gallery |
| **Split Screen** | Comparison/dual focus | Before/after, login |

```css
/* Card Grid - Responsive */
.grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(280px, 1fr));
  gap: 24px;
}

/* Holy Grail Layout */
.layout {
  display: grid;
  grid-template-rows: auto 1fr auto;
  grid-template-columns: 240px 1fr 300px;
  min-height: 100vh;
}
```

---

### 2. ✍️ Typography Excellence

#### Font Selection

| Category | Recommended Fonts | Use Case |
|----------|------------------|----------|
| **Sans-Serif** | Inter, SF Pro, Geist, Plus Jakarta Sans | UI text, body |
| **Monospace** | JetBrains Mono, Fira Code, SF Mono | Code, data |
| **Display** | Cabinet Grotesk, Clash Display | Headlines, hero |
| **Serif** | Source Serif Pro, Merriweather | Editorial content |

#### Type Scale (Minor Third: 1.2)

| Token | Size | Line Height | Weight | Use |
|-------|------|-------------|--------|-----|
| `text-xs` | 11px | 1.5 | 400 | Labels, captions |
| `text-sm` | 13px | 1.5 | 400 | Secondary text |
| `text-base` | 15px | 1.6 | 400 | Body text |
| `text-lg` | 17px | 1.5 | 500 | Lead paragraphs |
| `text-xl` | 20px | 1.4 | 600 | Subheadings |
| `text-2xl` | 24px | 1.3 | 600 | Section headings |
| `text-3xl` | 32px | 1.2 | 700 | Page titles |
| `text-4xl` | 40px | 1.1 | 700 | Hero headlines |
| `text-5xl` | 56px | 1.05 | 800 | Display text |

#### Typography Rules

```css
/* ✅ Premium Typography */
h1 {
  font-family: 'Inter', system-ui, sans-serif;
  font-size: 48px;
  font-weight: 700;
  letter-spacing: -0.03em;  /* Tighter for large text */
  line-height: 1.1;
  color: #111827;           /* Soft black */
}

p {
  font-size: 16px;
  font-weight: 400;
  line-height: 1.65;
  color: #374151;           /* Secondary text */
  max-width: 65ch;          /* Optimal reading width */
}

.small-text {
  font-size: 13px;
  letter-spacing: 0.01em;   /* Wider for small text */
  color: #6b7280;
}

/* ❌ AVOID */
h1 { color: #000; }         /* Too harsh */
p { line-height: 1.2; }     /* Too cramped */
p { max-width: 100%; }      /* Lines too long */
```

#### Font Loading Strategy

```css
/* Prevent layout shift with font-display */
@font-face {
  font-family: 'Inter';
  src: url('/fonts/Inter-Variable.woff2') format('woff2');
  font-weight: 100 900;
  font-display: swap;
  unicode-range: U+0000-00FF; /* Latin subset first */
}
```

---

### 3. 🎨 Color System

#### Semantic Color Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                    COLOR HIERARCHY                          │
├─────────────────────────────────────────────────────────────┤
│  BRAND                                                      │
│  └── Primary, Secondary, Accent                             │
│                                                             │
│  SEMANTIC                                                   │
│  └── Success, Warning, Error, Info                          │
│                                                             │
│  NEUTRAL                                                    │
│  └── Gray scale (50-950)                                    │
│                                                             │
│  SURFACE                                                    │
│  └── Background, Elevated, Overlay                          │
│                                                             │
│  TEXT                                                        │
│  └── Primary, Secondary, Muted, Disabled                    │
│                                                             │
│  BORDER                                                     │
│  └── Subtle, Default, Strong                                │
└─────────────────────────────────────────────────────────────┘
```

#### Color Palette (Light Theme)

```css
:root {
  /* Gray Scale - Tailwind-inspired */
  --gray-50: #f9fafb;
  --gray-100: #f3f4f6;
  --gray-200: #e5e7eb;
  --gray-300: #d1d5db;
  --gray-400: #9ca3af;
  --gray-500: #6b7280;
  --gray-600: #4b5563;
  --gray-700: #374151;
  --gray-800: #1f2937;
  --gray-900: #111827;
  --gray-950: #030712;

  /* Semantic - Text */
  --text-primary: var(--gray-900);
  --text-secondary: var(--gray-600);
  --text-muted: var(--gray-400);
  --text-disabled: var(--gray-300);

  /* Semantic - Backgrounds */
  --bg-primary: #ffffff;
  --bg-secondary: var(--gray-50);
  --bg-tertiary: var(--gray-100);
  --bg-elevated: #ffffff;
  --bg-overlay: rgba(0, 0, 0, 0.5);

  /* Semantic - States */
  --success: #10b981;
  --success-light: #d1fae5;
  --success-dark: #059669;
  
  --warning: #f59e0b;
  --warning-light: #fef3c7;
  --warning-dark: #d97706;
  
  --error: #ef4444;
  --error-light: #fee2e2;
  --error-dark: #dc2626;
  
  --info: #3b82f6;
  --info-light: #dbeafe;
  --info-dark: #2563eb;

  /* Borders */
  --border-subtle: rgba(0, 0, 0, 0.05);
  --border-default: rgba(0, 0, 0, 0.1);
  --border-strong: rgba(0, 0, 0, 0.2);
}
```

#### Color Contrast Requirements (WCAG 2.1)

| Element Type | Minimum Ratio | Level |
|--------------|---------------|-------|
| Body text | 4.5:1 | AA |
| Large text (18px+) | 3:1 | AA |
| UI components | 3:1 | AA |
| Decorative elements | No requirement | - |

**Testing Tool**: Use [WebAIM Contrast Checker](https://webaim.org/resources/contrastchecker/)

#### Dark Mode

```css
[data-theme="dark"] {
  --text-primary: #f9fafb;
  --text-secondary: #d1d5db;
  --text-muted: #6b7280;
  
  --bg-primary: #0a0a0a;
  --bg-secondary: #141414;
  --bg-tertiary: #1f1f1f;
  --bg-elevated: #1a1a1a;
  
  --border-subtle: rgba(255, 255, 255, 0.05);
  --border-default: rgba(255, 255, 255, 0.1);
  --border-strong: rgba(255, 255, 255, 0.2);
}
```

---

### 4. ⚡ Micro-Interactions & Animation

#### Animation Timing Guidelines

| Duration | Feel | Use Case |
|----------|------|----------|
| **50-100ms** | Instant | Button click, checkbox toggle |
| **150-200ms** | Fast | Hover states, focus rings |
| **250-300ms** | Standard | Dropdowns, tooltips, panels |
| **400-500ms** | Deliberate | Modals, page transitions |
| **600-1000ms** | Slow | Loading animations, celebrations |

#### Easing Functions

| Easing | CSS Value | When to Use |
|--------|-----------|-------------|
| **ease-out** | `cubic-bezier(0, 0, 0.2, 1)` | Elements entering (fade in, slide in) |
| **ease-in** | `cubic-bezier(0.4, 0, 1, 1)` | Elements leaving (fade out, slide out) |
| **ease-in-out** | `cubic-bezier(0.4, 0, 0.2, 1)` | State changes (expand/collapse) |
| **spring** | `cubic-bezier(0.34, 1.56, 0.64, 1)` | Playful interactions (bounce) |
| **smooth** | `cubic-bezier(0.22, 1, 0.36, 1)` | Page transitions |

#### Key Animation Patterns

```css
/* 1. BUTTON PRESS - Tactile feedback */
.btn {
  transition: transform 200ms ease-out, box-shadow 200ms ease-out;
}
.btn:hover {
  transform: translateY(-2px);
  box-shadow: 0 8px 16px rgba(0, 0, 0, 0.15);
}
.btn:active {
  transform: translateY(0) scale(0.98);
  transition-duration: 50ms;
}

/* 2. CARD HOVER - Subtle lift */
.card {
  transition: transform 300ms ease-out, box-shadow 300ms ease-out;
}
.card:hover {
  transform: translateY(-4px);
  box-shadow: 0 12px 24px rgba(0, 0, 0, 0.12);
}

/* 3. FADE IN - Staggered list */
@keyframes fadeInUp {
  from {
    opacity: 0;
    transform: translateY(16px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}
.list-item {
  animation: fadeInUp 400ms ease-out backwards;
}
.list-item:nth-child(1) { animation-delay: 0ms; }
.list-item:nth-child(2) { animation-delay: 50ms; }
.list-item:nth-child(3) { animation-delay: 100ms; }

/* 4. SKELETON SHIMMER - Loading state */
@keyframes shimmer {
  0% { background-position: -200% 0; }
  100% { background-position: 200% 0; }
}
.skeleton {
  background: linear-gradient(
    90deg,
    var(--gray-200) 25%,
    var(--gray-100) 50%,
    var(--gray-200) 75%
  );
  background-size: 200% 100%;
  animation: shimmer 1.5s infinite linear;
}

/* 5. FOCUS RING - Accessibility */
.focusable:focus-visible {
  outline: none;
  box-shadow: 
    0 0 0 2px var(--bg-primary),
    0 0 0 4px var(--info);
  transition: box-shadow 150ms ease-out;
}

/* 6. MODAL ENTRANCE */
@keyframes modalIn {
  from {
    opacity: 0;
    transform: scale(0.95) translateY(10px);
  }
  to {
    opacity: 1;
    transform: scale(1) translateY(0);
  }
}
.modal {
  animation: modalIn 300ms cubic-bezier(0.22, 1, 0.36, 1);
}

/* 7. TOOLTIP APPEARANCE */
.tooltip {
  opacity: 0;
  transform: translateY(4px);
  transition: opacity 150ms, transform 150ms;
  transition-timing-function: ease-out;
}
.trigger:hover .tooltip {
  opacity: 1;
  transform: translateY(0);
}
```

#### Animation Performance

```css
/* ✅ PERFORMANT - Uses compositor-only properties */
.good {
  transform: translateX(100px);
  opacity: 0.5;
}

/* ❌ EXPENSIVE - Triggers layout/paint */
.bad {
  left: 100px;      /* Triggers layout */
  width: 200px;     /* Triggers layout */
  background: red;  /* Triggers paint */
}

/* Use will-change sparingly */
.animated-element {
  will-change: transform, opacity;
}
```

---

### 5. ♿ Accessibility (A11y) Standards

#### The Four Principles (POUR)

| Principle | Meaning | Examples |
|-----------|---------|----------|
| **Perceivable** | Can users perceive the content? | Alt text, captions, contrast |
| **Operable** | Can users interact with it? | Keyboard nav, focus states |
| **Understandable** | Can users understand it? | Clear labels, error messages |
| **Robust** | Does it work everywhere? | Semantic HTML, ARIA |

#### Keyboard Navigation

| Key | Action |
|-----|--------|
| `Tab` | Move to next focusable element |
| `Shift + Tab` | Move to previous focusable element |
| `Enter` / `Space` | Activate button/link |
| `Escape` | Close modal/dropdown |
| `Arrow Keys` | Navigate within component (dropdown, tabs) |

```tsx
// Keyboard Navigation Example
function Dropdown({ options, onSelect }) {
  const [isOpen, setIsOpen] = useState(false);
  const [activeIndex, setActiveIndex] = useState(0);

  const handleKeyDown = (e: KeyboardEvent) => {
    switch (e.key) {
      case 'ArrowDown':
        e.preventDefault();
        setActiveIndex(i => Math.min(i + 1, options.length - 1));
        break;
      case 'ArrowUp':
        e.preventDefault();
        setActiveIndex(i => Math.max(i - 1, 0));
        break;
      case 'Enter':
      case ' ':
        e.preventDefault();
        onSelect(options[activeIndex]);
        setIsOpen(false);
        break;
      case 'Escape':
        e.preventDefault();
        setIsOpen(false);
        break;
    }
  };

  return (
    <div
      role="combobox"
      aria-expanded={isOpen}
      aria-haspopup="listbox"
      aria-activedescendant={`option-${activeIndex}`}
      onKeyDown={handleKeyDown}
      tabIndex={0}
    >
      {/* ... */}
    </div>
  );
}
```

#### Focus Management

```tsx
// Modal Focus Trap
function Modal({ isOpen, onClose, children }) {
  const modalRef = useRef<HTMLDivElement>(null);
  const previousFocus = useRef<HTMLElement | null>(null);

  useEffect(() => {
    if (isOpen) {
      // Store current focus
      previousFocus.current = document.activeElement as HTMLElement;
      
      // Focus first focusable element
      const focusable = modalRef.current?.querySelector<HTMLElement>(
        'button, [href], input, select, textarea, [tabindex]:not([tabindex="-1"])'
      );
      focusable?.focus();
    } else {
      // Restore focus on close
      previousFocus.current?.focus();
    }
  }, [isOpen]);

  // Focus trap implementation
  const handleKeyDown = (e: KeyboardEvent) => {
    if (e.key === 'Tab') {
      const focusableElements = modalRef.current?.querySelectorAll<HTMLElement>(
        'button, [href], input, select, textarea, [tabindex]:not([tabindex="-1"])'
      );
      
      if (!focusableElements?.length) return;
      
      const first = focusableElements[0];
      const last = focusableElements[focusableElements.length - 1];

      if (e.shiftKey && document.activeElement === first) {
        e.preventDefault();
        last.focus();
      } else if (!e.shiftKey && document.activeElement === last) {
        e.preventDefault();
        first.focus();
      }
    }
  };

  return (
    <div
      ref={modalRef}
      role="dialog"
      aria-modal="true"
      aria-labelledby="modal-title"
      onKeyDown={handleKeyDown}
    >
      {children}
    </div>
  );
}
```

#### ARIA Patterns

```html
<!-- Button with loading state -->
<button 
  aria-busy="true" 
  aria-disabled="true"
  aria-label="Saving changes..."
>
  <span class="spinner" aria-hidden="true"></span>
  Saving...
</button>

<!-- Expandable section -->
<button 
  aria-expanded="false" 
  aria-controls="section-content"
>
  Show Details
</button>
<div id="section-content" hidden>
  Content here
</div>

<!-- Live region for announcements -->
<div aria-live="polite" aria-atomic="true" class="sr-only">
  Form submitted successfully
</div>

<!-- Tab list -->
<div role="tablist" aria-label="User settings">
  <button role="tab" aria-selected="true" aria-controls="panel-1">
    Profile
  </button>
  <button role="tab" aria-selected="false" aria-controls="panel-2">
    Settings
  </button>
</div>
<div role="tabpanel" id="panel-1" aria-labelledby="tab-1">
  Profile content
</div>
```

#### Screen Reader Only Content

```css
.sr-only {
  position: absolute;
  width: 1px;
  height: 1px;
  padding: 0;
  margin: -1px;
  overflow: hidden;
  clip: rect(0, 0, 0, 0);
  white-space: nowrap;
  border-width: 0;
}
```

---

### 6. 📦 Loading & Empty States

#### Loading State Hierarchy

| Priority | State | When |
|----------|-------|------|
| 1 | **Optimistic UI** | Assume success, rollback on error |
| 2 | **Skeleton Loaders** | Known content shape |
| 3 | **Progress Indicators** | Known duration |
| 4 | **Spinners** | Unknown duration, small area |
| 5 | **Full-page Loaders** | Initial app load only |

#### Skeleton Loader Example

```tsx
function SkeletonCard() {
  return (
    <div className="card skeleton-container">
      {/* Avatar */}
      <div className="skeleton skeleton-circle" style={{ width: 48, height: 48 }} />
      
      {/* Content */}
      <div className="skeleton-content">
        <div className="skeleton skeleton-text" style={{ width: '60%' }} />
        <div className="skeleton skeleton-text" style={{ width: '80%' }} />
        <div className="skeleton skeleton-text" style={{ width: '40%' }} />
      </div>
    </div>
  );
}

// CSS
.skeleton {
  background: linear-gradient(
    90deg,
    hsl(0 0% 93%) 0%,
    hsl(0 0% 97%) 50%,
    hsl(0 0% 93%) 100%
  );
  background-size: 200% 100%;
  animation: shimmer 1.5s infinite;
  border-radius: 4px;
}

.skeleton-text {
  height: 14px;
  margin-bottom: 8px;
}

.skeleton-circle {
  border-radius: 50%;
}
```

#### Empty State Components

```tsx
interface EmptyStateProps {
  icon: React.ReactNode;
  title: string;
  description: string;
  action?: {
    label: string;
    onClick: () => void;
  };
}

function EmptyState({ icon, title, description, action }: EmptyStateProps) {
  return (
    <div className="empty-state">
      <div className="empty-state-icon">{icon}</div>
      <h3 className="empty-state-title">{title}</h3>
      <p className="empty-state-description">{description}</p>
      {action && (
        <button className="btn btn-primary" onClick={action.onClick}>
          {action.label}
        </button>
      )}
    </div>
  );
}

// Usage
<EmptyState
  icon={<InboxIcon />}
  title="No messages yet"
  description="Messages from your team will appear here when they send them."
  action={{
    label: "Invite teammates",
    onClick: () => openInviteModal()
  }}
/>
```

#### Error State Patterns

```tsx
// Error Boundary with Recovery
function ErrorFallback({ error, resetErrorBoundary }) {
  return (
    <div className="error-state">
      <div className="error-icon">
        <AlertTriangleIcon />
      </div>
      <h3>Something went wrong</h3>
      <p className="error-message">{error.message}</p>
      <div className="error-actions">
        <button onClick={resetErrorBoundary} className="btn btn-primary">
          Try again
        </button>
        <button onClick={() => window.location.reload()} className="btn btn-secondary">
          Refresh page
        </button>
      </div>
      <details className="error-details">
        <summary>Technical details</summary>
        <pre>{error.stack}</pre>
      </details>
    </div>
  );
}
```

---

### 7. 📱 Responsive Excellence

#### Breakpoint System

| Token | Width | Target |
|-------|-------|--------|
| `xs` | 0-639px | Mobile portrait |
| `sm` | 640-767px | Mobile landscape |
| `md` | 768-1023px | Tablet |
| `lg` | 1024-1279px | Desktop |
| `xl` | 1280-1535px | Large desktop |
| `2xl` | 1536px+ | Ultra-wide |

#### Mobile-First CSS

```css
/* Base styles = mobile */
.container {
  padding: 16px;
}

.grid {
  display: flex;
  flex-direction: column;
  gap: 16px;
}

/* Tablet and up */
@media (min-width: 768px) {
  .container {
    padding: 24px;
  }
  
  .grid {
    display: grid;
    grid-template-columns: repeat(2, 1fr);
    gap: 24px;
  }
}

/* Desktop and up */
@media (min-width: 1024px) {
  .container {
    padding: 32px;
    max-width: 1200px;
    margin: 0 auto;
  }
  
  .grid {
    grid-template-columns: repeat(3, 1fr);
  }
}
```

#### Touch-Friendly Design

| Requirement | Minimum | Recommended |
|-------------|---------|-------------|
| Touch target | 44×44px | 48×48px |
| Spacing between targets | 8px | 16px |
| Button padding | 12px | 16px |

```css
/* Touch-optimized button */
.btn-mobile {
  min-height: 48px;
  min-width: 48px;
  padding: 12px 24px;
}

/* Increase tap area without visual change */
.small-button {
  position: relative;
}
.small-button::before {
  content: '';
  position: absolute;
  inset: -8px; /* Expands clickable area */
}
```

#### Responsive Typography

```css
/* Fluid typography using clamp() */
h1 {
  font-size: clamp(28px, 5vw, 56px);
}

h2 {
  font-size: clamp(24px, 4vw, 40px);
}

p {
  font-size: clamp(15px, 2vw, 18px);
}
```

#### Container Queries (Modern CSS)

```css
/* Define container */
.card-container {
  container-type: inline-size;
}

/* Respond to container, not viewport */
@container (min-width: 300px) {
  .card {
    display: grid;
    grid-template-columns: 100px 1fr;
  }
}

@container (min-width: 500px) {
  .card {
    grid-template-columns: 150px 1fr auto;
  }
}
```

---

## 🎯 Component-Specific Guidelines

### Buttons

| Variant | Use Case | Visual Treatment |
|---------|----------|------------------|
| **Primary** | Main action | Filled, accent color |
| **Secondary** | Alternative action | Outlined or ghost |
| **Tertiary** | Low-priority | Text-only, minimal |
| **Destructive** | Delete, cancel | Red/error color |
| **Ghost** | Toolbar, icon buttons | No background until hover |

```css
.btn {
  /* Base */
  display: inline-flex;
  align-items: center;
  justify-content: center;
  gap: 8px;
  padding: 10px 16px;
  min-height: 40px;
  
  /* Typography */
  font-size: 14px;
  font-weight: 500;
  line-height: 1;
  
  /* Visual */
  border-radius: 8px;
  border: 1px solid transparent;
  
  /* Interaction */
  cursor: pointer;
  transition: all 200ms ease-out;
}

.btn:disabled {
  opacity: 0.5;
  cursor: not-allowed;
  pointer-events: none;
}
```

### Form Inputs

```css
.input {
  /* Layout */
  display: block;
  width: 100%;
  padding: 10px 12px;
  min-height: 40px;
  
  /* Typography */
  font-size: 15px;
  line-height: 1.5;
  
  /* Visual */
  background: var(--bg-primary);
  border: 1px solid var(--border-default);
  border-radius: 8px;
  
  /* Interaction */
  transition: border-color 150ms, box-shadow 150ms;
}

.input:hover {
  border-color: var(--border-strong);
}

.input:focus {
  outline: none;
  border-color: var(--info);
  box-shadow: 0 0 0 3px var(--info-light);
}

.input[aria-invalid="true"] {
  border-color: var(--error);
}

.input[aria-invalid="true"]:focus {
  box-shadow: 0 0 0 3px var(--error-light);
}
```

### Cards

```css
.card {
  background: var(--bg-elevated);
  border: 1px solid var(--border-subtle);
  border-radius: 12px;
  padding: 24px;
  
  /* Subtle shadow for elevation */
  box-shadow: 
    0 1px 3px rgba(0, 0, 0, 0.04),
    0 6px 16px rgba(0, 0, 0, 0.04);
  
  transition: transform 300ms ease-out, box-shadow 300ms ease-out;
}

.card:hover {
  transform: translateY(-4px);
  box-shadow: 
    0 4px 12px rgba(0, 0, 0, 0.08),
    0 12px 32px rgba(0, 0, 0, 0.08);
}
```

### Modals

```css
.modal-overlay {
  position: fixed;
  inset: 0;
  background: rgba(0, 0, 0, 0.5);
  backdrop-filter: blur(4px);
  display: flex;
  align-items: center;
  justify-content: center;
  padding: 24px;
  
  animation: fadeIn 200ms ease-out;
}

.modal-content {
  background: var(--bg-elevated);
  border-radius: 16px;
  width: 100%;
  max-width: 480px;
  max-height: 90vh;
  overflow: hidden;
  
  box-shadow: 
    0 0 0 1px var(--border-subtle),
    0 16px 48px rgba(0, 0, 0, 0.24);
  
  animation: modalIn 300ms cubic-bezier(0.22, 1, 0.36, 1);
}

@keyframes modalIn {
  from {
    opacity: 0;
    transform: scale(0.95) translateY(20px);
  }
}
```

---

## 🔍 AAA Quality Checklist

### Pre-Flight Check

```markdown
## Visual
- [ ] All spacing follows 8px grid
- [ ] Typography has clear 3-4 level hierarchy
- [ ] Colors meet WCAG AA contrast (4.5:1 text, 3:1 UI)
- [ ] Consistent border radius across components
- [ ] No pixel-misaligned elements

## Interaction
- [ ] All buttons have hover + active states
- [ ] All inputs have focus + error states
- [ ] Transitions are 150-300ms with proper easing
- [ ] No jarring instant state changes
- [ ] Touch targets are 44px minimum

## Accessibility
- [ ] Keyboard navigation works for all flows
- [ ] Focus rings visible on all interactive elements
- [ ] Proper ARIA labels on icon-only buttons
- [ ] Color is not the only indicator of state
- [ ] Screen reader tested main flows

## States
- [ ] Loading states for all async operations
- [ ] Error states with recovery actions
- [ ] Empty states with helpful CTAs
- [ ] Disabled states are visually clear

## Responsive
- [ ] Mobile (320px) tested and functional
- [ ] Tablet (768px) layout appropriate
- [ ] Desktop (1024px+) uses space well
- [ ] No horizontal scroll on any viewport
- [ ] Touch-friendly on mobile

## Performance
- [ ] Animations are 60fps smooth
- [ ] No layout shift on load
- [ ] Images have proper sizing/loading
- [ ] Critical CSS is inline/fast
```

---

## 🚫 Common AAA Violations

| Issue | Problem | Fix |
|-------|---------|-----|
| `color: #000` | Too harsh on eyes | Use `#111827` or `--gray-900` |
| `color: #fff` on white | Poor contrast | Check background context |
| No hover state | Feels unresponsive | Add background/transform change |
| Instant transitions | Jarring experience | Add 150-200ms transition |
| `padding: 13px` | Off-grid value | Use `padding: 12px` or `16px` |
| Fixed font sizes | Accessibility issue | Use responsive/fluid sizing |
| No focus styles | Keyboard users lost | Add visible focus ring |
| Empty state is blank | Confusing, dead-end | Add illustration + CTA |
| Generic errors | Unhelpful | Be specific, offer recovery |
| 1px gray borders everywhere | Looks dated | Use subtle shadows, less borders |

---

## 📚 Reference Resources

### Design Inspiration
- [Linear](https://linear.app) - Issue tracking perfection
- [Stripe](https://stripe.com) - Payment UI excellence
- [Vercel](https://vercel.com) - Developer platform
- [Raycast](https://raycast.com) - Command bar perfection
- [Figma](https://figma.com) - Design tool UI
- [Notion](https://notion.so) - Productivity app

### Tools
- [Contrast Checker](https://webaim.org/resources/contrastchecker/)
- [Type Scale](https://typescale.com/)
- [Coolors](https://coolors.co/) - Color palette generator
- [Cubic Bezier](https://cubic-bezier.com/) - Easing visualizer
- [Screen Reader Testing](https://www.nvaccess.org/download/)

### Documentation
- [WCAG 2.1 Guidelines](https://www.w3.org/WAI/WCAG21/quickref/)
- [MDN ARIA Authoring Practices](https://developer.mozilla.org/en-US/docs/Web/Accessibility/ARIA)
- [Google Material Design](https://material.io/design)

---

## 💬 Guiding Principles

> **"Good design is obvious. Great design is transparent."**  
> — Joe Sparano

> **"Details are not the details. They make the design."**  
> — Charles Eames

> **"Design is not just what it looks like and feels like. Design is how it works."**  
> — Steve Jobs

### Three Questions Before Every UI Decision

1. **Would Linear/Stripe do this?** (Quality bar)
2. **Is this the simplest solution?** (Complexity check)
3. **Does it feel instant and responsive?** (Performance check)

---

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/atlasrox) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
