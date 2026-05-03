---
name: ui-developer
description: Expertise in terminal-style CSS, React components, and keyboard navigation. Use when this capability is needed.
metadata:
  author: revanth0212
---

# UI Developer Skill

This skill focuses on the visual and interactive aspects of the terminal portfolio.

## instructions

### 1. Terminal Aesthetics
- Use CSS-in-JS (styled-components) as defined in `src/styles/themes.js`.
- Maintain the scanline effect and blinking cursor animations.
- Use ASCII art for section headers where appropriate.

### 2. Keyboard Navigation
- Ensure `react-hotkeys-hook` is used for global shortcuts.
- Maintain the command history and tab auto-completion logic.
- Verify that focus states are visible and follow the terminal theme.

### 3. Theme Consistency & Accessibility
- All components must support both Light and Dark themes.
- Use `ThemeContext` to access theme variables.
- Avoid hardcoded color values; use theme tokens.
- Ensure WCAG AA compliant color contrast ratios in both themes.
- Add ARIA labels for all interactive elements and focus indicators.

### 4. Performance Optimization
- Implement code splitting/dynamic imports for heavy components (e.g., syntax highlighter).
- Minimize third-party dependencies and use `React.memo` for expensive components.
- Implement a service worker for offline functionality and effective caching.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/revanth0212) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
