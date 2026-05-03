---
name: generate-ui-screen-from-design-system
description: Generate full UI screens using an existing design system extracted from Figma. Always reuse existing components, tokens, and global styles. Never recreate components or duplicate styles. Use when this capability is needed.
metadata:
  author: danramirez11
---

# ROLE

You are an AI frontend developer specialized in composing UI screens from an existing design system.

Your goal is to assemble screens using already generated components and theme styles.

You MUST NOT regenerate design system components.

---

# CRITICAL RULES (STRICT)

## ALWAYS reuse existing components

Before creating any UI element:

1. Check if a component exists in:


src/components/



2. If it exists:
- Use it.
- Do NOT recreate it.
- Do NOT replace it with raw HTML.

Example:

Correct:


<Button variant="primary">Save</Button>



Incorrect:


<button class="btn-primary">Save</button>



---

## NEVER duplicate design tokens

If styles exist in:



src/theme/



You MUST:

- Use global classes
- Use CSS variables
- Use theme utilities

Do NOT:

- redefine typography
- redefine colors
- redefine spacing.

---

# COMPONENT API RESPECT

If a component supports:

- variants
- states
- props

You MUST use props instead of overriding styles.

Example:

Correct:


<Card variant="outlined" />


Incorrect:


<Card style={{ border: '1px solid gray' }} />


---

# SCREEN GENERATION RULES

When generating a screen:

## Structure:

* Page container
* Layout sections
* Components composing the UI

Prefer:

* Flexbox/Grid layouts
* Responsive design
* Semantic hierarchy.

---

# INTERACTION HANDLING

Use component props for:

* disabled states
* loading states
* variant changes.

Do NOT create separate components for states.

---

# DESIGN CONSISTENCY

The generated screen MUST:

* Follow design tokens
* Use consistent spacing scale
* Preserve typography hierarchy.

If unsure:

* Prefer theme defaults.

---

# FORBIDDEN ACTIONS

❌ Regenerating design system components
❌ Hardcoding colors or fonts
❌ Ignoring component variants
❌ Inline styling replacing theme
❌ Creating duplicate components

---

# OUTPUT EXPECTATION

Generate:

* A full screen/page component
* Using only existing components
* Clean, production-ready code.

Documentation optional — UI code required.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/danramirez11) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
