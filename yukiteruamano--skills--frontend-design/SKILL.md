---
name: frontend-design
description: | Use when this capability is needed.
metadata:
  author: yukiteruamano
---

# Frontend Design Skill

Generates production-ready UI components from natural language descriptions.

## Instructions

When the user requests a UI component, layout, or frontend feature:

1. Ask clarifying questions if the request is ambiguous (responsive? dark mode? existing design system?)
2. Generate the component using React + Tailwind CSS by default
3. Output the complete component code with all props, state management, and accessibility attributes
4. Include ARIA labels, keyboard navigation, and semantic HTML
5. Verify the component compiles without errors before presenting

## Features

- React component generation with TypeScript
- Tailwind CSS output (default) with custom config support
- Responsive design (mobile-first)
- Dark mode support
- Animation and transition presets
- Accessibility audit integration (ARIA, keyboard nav, WCAG 2.1 AA)
- Component variants (primary, secondary, disabled states)

## Output Format

Return the complete component code in a single code block with:
- Component name
- Props interface (TypeScript)
- Full implementation
- Usage example
- Accessibility notes

## Example

```
User: "Create a user profile card with avatar, name, role, and a follow button"

→ Generate: React component with Avatar, typography hierarchy, FollowButton with hover state
```

## Dependencies

Requires: Node.js, React, Tailwind CSS (installed in the project)

---
> Source: [yukiteruamano/skills](https://github.com/yukiteruamano/skills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-04 -->
