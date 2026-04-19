---
name: component-create
description: Create new React components following project patterns. Use when user asks to "create component", "add a new section", "build a component", or "new feature". Use when this capability is needed.
metadata:
  author: aaryan126
---

# Component Creation Skill

Create React components that match this portfolio's established patterns.

## Project Structure

```
src/
├── components/       # React components
│   ├── Navbar.jsx
│   ├── Hero.jsx
│   ├── About.jsx
│   ├── Experience.jsx
│   ├── Skills.jsx
│   ├── Projects.jsx
│   ├── Contact.jsx
│   └── SkillBar.jsx
├── hooks/            # Custom hooks
│   ├── useTypewriter.js
│   ├── useInView.js
│   ├── useCountUp.js
│   └── useActiveSection.js
├── context/
│   └── ThemeContext.jsx
├── App.jsx
├── main.jsx
└── index.css         # All styles
```

## Component Template

```jsx
import { useInView } from '../hooks/useInView';

const ComponentName = () => {
  const [ref, isInView] = useInView({ threshold: 0.1 });

  return (
    <section id="section-name" className="section-name" ref={ref}>
      <div className="container">
        <h2 className="section-title">Section Title</h2>
        {/* Content */}
      </div>
    </section>
  );
};

export default ComponentName;
```

## Styling Pattern

All styles go in `src/index.css`. Follow this structure:

```css
/* ==========================================================================
   Component Name
   ========================================================================== */

.component-name {
  padding: var(--section-padding);
}

.component-name .container {
  max-width: var(--container-width);
  margin: 0 auto;
  padding: 0 var(--container-padding);
}

/* Dark mode */
[data-theme="dark"] .component-name {
  /* Dark mode overrides */
}

/* Animations */
.component-name.in-view .element {
  animation: fadeInUp 0.6s ease-out forwards;
}

/* Responsive */
@media (max-width: 768px) {
  .component-name {
    /* Mobile styles */
  }
}
```

## Available CSS Variables

```css
/* Use these existing variables */
--primary-color
--secondary-color
--text-color
--text-secondary
--bg-color
--bg-secondary
--border-color
--shadow
--section-padding
--container-width
--container-padding
--transition
--border-radius
```

## Available Hooks

### useInView
Detect when element enters viewport:
```jsx
const [ref, isInView] = useInView({ threshold: 0.1 });
```

### useTypewriter
Typewriter text effect:
```jsx
const text = useTypewriter(phrases, 100, 50, 2000);
```

### useCountUp
Animate numbers:
```jsx
const count = useCountUp(targetNumber, duration, isInView);
```

### useActiveSection
Track scroll position for nav highlighting:
```jsx
const activeSection = useActiveSection(sectionIds);
```

## Checklist for New Components

- [ ] Create functional component with proper naming
- [ ] Add section ID for navigation (if main section)
- [ ] Use `useInView` hook for scroll animations
- [ ] Add styles to `index.css` with component comment header
- [ ] Include dark mode styles with `[data-theme="dark"]`
- [ ] Add responsive breakpoints
- [ ] Use existing CSS variables
- [ ] Export and import in `App.jsx` if needed
- [ ] Follow existing animation patterns

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/aaryan126) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
