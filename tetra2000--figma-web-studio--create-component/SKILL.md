---
name: create-component
description: Generate React components with Tailwind CSS classes and Storybook stories from a natural language description. Use when user says "create component", "make a component", "build a component", "new component", or describes a UI element they want to create. Use when this capability is needed.
metadata:
  author: TETRA2000
---

# Create Component Skill

## Parameters

- **description** (required): Natural language description of the component to create
- **referenceUrl** (optional): URL of a website to use as visual reference for design inspiration
- **scope** (optional): `repository` or `local` (default: ask user)

## Workflow

### 1. Capture Reference Website (if referenceUrl provided)

When the user provides a reference URL (or mentions a website they want to base the design on):

1. Open the browser and navigate to the reference URL:
   ```bash
   playwright-cli open {referenceUrl}
   ```
2. Take a screenshot to capture the visual design:
   ```bash
   playwright-cli screenshot --filename=reference-design.png
   ```
3. Take a snapshot to extract the page structure and element layout:
   ```bash
   playwright-cli snapshot --filename=reference-structure.yaml
   ```
4. If a specific element on the page matches the component being created, screenshot just that element using its ref:
   ```bash
   playwright-cli screenshot {ref} --filename=reference-element.png
   ```
5. Close the browser:
   ```bash
   playwright-cli close
   ```

Use the captured screenshot and structure as design inspiration — adapt the visual style (colors, spacing, typography, layout patterns) to the project's Tailwind theme tokens rather than copying arbitrary values.

### 2. Gather Context

- Read `shared/references/component-registry.md` for existing components
- Read `shared/references/tailwind-theme.md` for available design tokens
- Read `shared/references/figma-compat-rules.md` for validation rules

### 3. Determine Scope

If scope not specified, ask the user:
- **repository** → `src/components/{Name}/` (committed to Git)
- **local** → `src/components/_local/{Name}/` (Git-ignored)

### 4. Generate Component

Create two files:

#### `{Name}.tsx`
- Use named export (not default export)
- Extend appropriate HTML element attributes via `HTMLAttributes` or specific element interface
- Use Tailwind CSS utility classes from the theme
- Define TypeScript interface for props with JSDoc comments
- Support variants via prop types

#### `{Name}.stories.tsx`
- Import from `@storybook/react`
- Use `Meta` and `StoryObj` types
- Include controls for all props
- Create stories for each variant combination
- Use `satisfies Meta<typeof Component>` pattern

### 5. Validate Figma Compatibility

Scan the generated TSX for prohibited patterns:
- Arbitrary values: `[#...]`, `[url(...)]`, `[13px]`
- Position utilities: `absolute`, `fixed`, `sticky`
- Animation utilities: `animate-`, `transition-` (except `transition-colors`)
- Pseudo variants: `before:`, `after:` for layout
- Responsive prefixes: `sm:`, `md:`, `lg:`
- Negative margins: `-m-*`

Report findings as warnings. Do NOT block creation.

### 6. Update Indexes

- Add entry to `docs/components.md` (repository) or `docs/_local/components.md` (local)
- Add entry to `shared/references/component-registry.md`

Format for component index:
```markdown
## {Name}
- **Path**: `src/components/{Name}/`
- **Description**: {description}
- **Props**: {prop list}
- **Variants**: {variant list}
- **Figma**: —
```

### 7. Preview

- Launch Storybook preview for visual verification
- Use `playwright-cli` to capture a screenshot if available

## Output

Report:
1. Files created (paths)
2. Validation warnings (if any)
3. Index documents updated
4. Screenshot captured (if applicable)

## Example

User: "Create a Card component with a title, description, image, and optional footer. Support outlined and filled variants."

Creates:
- `src/components/Card/Card.tsx` — React component with Tailwind
- `src/components/Card/Card.stories.tsx` — Storybook stories
- Updates `docs/components.md` and component registry

User: "Create a pricing card component like the ones on https://stripe.com/pricing"

Captures the reference website via playwright-cli, then creates:
- `src/components/PricingCard/PricingCard.tsx` — Inspired by Stripe's card design, adapted to project theme
- `src/components/PricingCard/PricingCard.stories.tsx` — Storybook stories
- Updates `docs/components.md` and component registry

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/TETRA2000) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
