---
name: figma-to-component
description: Interpret Figma design exports and build matching components using design system tokens Use when this capability is needed.
metadata:
  author: tasty-maker-studio
---

# Figma to Component Workflow

## When to Use This Skill

- Building a new component from Figma mockups
- Matching visual design specifications to implementation
- Translating designer intent into semantic token usage
- Any time `docs/ux/{component-name}/` contains reference images

## Image Location Convention

```
docs/ux/
├── stepper/
│   ├── Stepper.jpg        # Component states/variants
│   └── Step1.jpg          # In-context usage example
├── card/
│   ├── Card.jpg
│   └── CardVariants.jpg
└── {component-name}/
    └── {descriptive-name}.jpg
```

### Naming Conventions

- `{ComponentName}.jpg` - Primary component view showing all states
- `{State}.jpg` or `Step{N}.jpg` - Specific state or step in a flow
- `{ComponentName}Variants.jpg` - All variant options
- `{ComponentName}InContext.jpg` - Component used in a real screen

## Interpretation Process

### Step 1: Analyze the Design

Before writing code, examine the Figma exports and document:

1. **States** - What visual states exist? (active, inactive, completed, disabled, hover, etc.)
2. **Variants** - Are there size/style/color variants?
3. **Structure** - What are the sub-elements? (icon, label, connector, container, etc.)
4. **Spacing** - Relative proportions between elements
5. **Typography** - Text sizes, weights, styles used
6. **Colors** - Which colors map to which states

### Step 2: Map Colors to Semantic Tokens

Never use raw hex values. Always map Figma colors to existing semantic tokens:

| Visual Appearance           | Likely Semantic Token           | Usage                                |
| --------------------------- | ------------------------------- | ------------------------------------ |
| Brand green (filled)        | `primary`                       | Active/current state, primary action |
| Brand green (light/outline) | `primaryContainer`              | Secondary emphasis, borders          |
| White on green              | `onPrimary`                     | Text/icons on primary background     |
| Gray/muted fill             | `surfaceContainerHigh`          | Inactive/future state background     |
| Gray border                 | `outline` or `outlineVariant`   | Borders, separators                  |
| Dark purple/gray            | `onSurfaceVariant`              | Completed state, secondary text      |
| Near-black text             | `onSurface`                     | Primary text                         |
| Light background            | `surface` or `surfaceContainer` | Container backgrounds                |

If uncertain, check `src/languages/material3.language.ts` for available semantic colors.

### Step 3: Map Spacing to Tokens

Estimate spacing from visual proportions and use spacing scale:

| Visual Size  | Token       | Pixels (approx) |
| ------------ | ----------- | --------------- |
| Tiny gap     | `spacing.1` | 4px             |
| Small gap    | `spacing.2` | 8px             |
| Medium gap   | `spacing.3` | 12px            |
| Standard gap | `spacing.4` | 16px            |
| Large gap    | `spacing.6` | 24px            |
| Section gap  | `spacing.8` | 32px            |

### Step 4: Map Typography

Use existing text styles from the design language:

| Visual Style    | Text Style Token           |
| --------------- | -------------------------- |
| Large heading   | `textStyles.headlineLarge` |
| Section heading | `textStyles.headlineSmall` |
| Body text       | `textStyles.bodyLarge`     |
| Small/caption   | `textStyles.bodySmall`     |
| Button text     | `textStyles.labelLarge`    |
| Tiny label      | `textStyles.labelSmall`    |

### Step 5: Identify Component API

From the design, determine:

```typescript
// What props does this component need?
interface ComponentProps {
  // From variants/states visible in Figma:
  variant?: 'default' | 'outlined' | 'filled';
  size?: 'sm' | 'md' | 'lg';

  // From interactive states:
  disabled?: boolean;

  // From content slots:
  children?: React.ReactNode;
  icon?: React.ReactNode;

  // From behavioral needs:
  onClick?: () => void;
}
```

## Example: Analyzing a Stepper Component

Given these Figma exports showing a horizontal stepper:

**Observations:**

1. Four steps with numbered circles connected by lines
2. Three visual states per step:
   - **Current**: Large green filled circle, bold number
   - **Completed**: Smaller purple/dark filled circle, italic number
   - **Upcoming**: Light gray outlined circle, normal number
3. Connecting lines are light green
4. Equal spacing between steps

**Token Mapping:**

```typescript
// States
current: {
  background: 'primary',        // Green fill
  text: 'onPrimary',           // White number
  size: 'lg',                  // Larger circle
}
completed: {
  background: 'onSurfaceVariant', // Purple/dark fill
  text: 'surface',               // Light number
  size: 'md',                    // Standard circle
}
upcoming: {
  background: 'surfaceContainerHigh', // Light gray fill
  border: 'outlineVariant',           // Subtle border
  text: 'onSurfaceVariant',           // Gray number
  size: 'md',                         // Standard circle
}
connector: {
  color: 'primaryContainer',    // Light green line
}
```

**Derived API:**

```typescript
interface StepperProps {
  steps: number; // Total step count
  currentStep: number; // 1-indexed current step
  size?: 'sm' | 'md' | 'lg';
}
```

## Integration with Other Skills

After analyzing the Figma design, use these skills to implement:

1. **panda-recipes** - Create the recipe with variants matching Figma states
2. **component-patterns** - Structure the React component properly
3. **m3-tokens** - Ensure correct token references

## Workflow Command Integration

When using `/new-component {name}`:

1. First check if `docs/ux/{name}/` exists
2. If yes, load this skill and analyze images before implementing
3. Document the Figma-to-token mapping in a comment block at top of recipe file
4. Implement component matching the analyzed design

## Quality Checklist

Before considering the component complete:

- [ ] All Figma states are represented as recipe variants or props
- [ ] Colors use semantic tokens (no raw hex values)
- [ ] Spacing uses spacing scale tokens
- [ ] Typography uses text style tokens
- [ ] Component renders visually identical to Figma at each state
- [ ] Storybook stories demonstrate each state from Figma
- [ ] Dark mode works correctly (semantic tokens handle this automatically)

## Files to Reference

- `docs/ux/{component}/` - Figma exports for the component
- `src/languages/material3.language.ts` - Available semantic token values
- `.claude/skills/m3-tokens/skill.md` - Token usage patterns
- `.claude/skills/panda-recipes/skill.md` - Recipe structure
- `.claude/skills/component-patterns/skill.md` - React component patterns

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/tasty-maker-studio) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
