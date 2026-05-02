---
name: material-thinking
description: Build project-level design systems based on Material Design principles. Use when creating a new design system, extracting one from an existing product, extending tokens or components, or reviewing UI for design system compliance. Outputs a project-level Agent Skill with token definitions and component specifications. Use when this capability is needed.
metadata:
  author: kurogoma4d
---

# Material Thinking

Build project-level design systems based on Material Design principles. Output a project-level Agent Skill (`.claude/skills/design-system/`) with design system documentation, token definitions, and component specifications.

## Premise

Material's essence is design principles, not a component library. This skill applies Material principles as methodology to:

1. Define brand identity (semantic + perceptual layers)
2. Translate brand into design tokens with documented rationale
3. Design component patterns following project requirements
4. Output an installable Agent Skill for the project

A design system is not a style guide. The difference is documenting **why each value was chosen** — connecting semantic brand traits to concrete token values.

## References

### 1. Brand Identity

Methodology for defining brand identity and translating it to design system elements: semantic elements (personality, voice & tone, values, relationship), perceptual elements (color, typography, spacing, shape, icons, motion), and the translation matrix between them.

- For full details, see [brand-identity.md](references/brand-identity.md)
- Refer to when defining brand, mapping semantic traits to perceptual elements, or documenting design rationale

### 2. Design System Methodology

Methodology for designing, building, and operating design systems: 5-layer structure, 3-tier token architecture (Reference → System → Component), component design principles, pattern organization, governance.

- For full details, see [design-system-methodology.md](references/design-system-methodology.md)
- Refer to when designing token architecture, applying component principles, organizing patterns, or governing the system

### 3. Foundations

Material's foundational principles: accessibility, layout (window size classes, canonical layouts), interaction (states, gestures, selection), content design, adaptive design.

- For full details, see [foundations.md](references/foundations.md)
- Refer to when checking accessibility, designing layout/responsive behavior, defining interaction patterns, or writing UX copy

### 4. Visual Styles

Visual language elements: color system (tonal palettes, color roles, dynamic color), typography (type scale, fonts), elevation, shape, icons, motion.

- For full details, see [styles.md](references/styles.md)
- Refer to when designing color systems, defining typography, working with elevation/shape, or designing motion

### 5. Expressive Expression

Techniques for creating more expressive UIs: emphasized easing, extended duration, exaggerated scale, dynamic color, layered motion, shape morphing. Includes the 80/20 rule and accessibility considerations.

- For full details, see [m3-expressive.md](references/m3-expressive.md)
- Refer to when enhancing brand expression or balancing expressiveness with usability

## Workflows

### Workflow 1: Build a New Project Design System

Full workflow from brand definition to Agent Skill output.

#### 1. Define brand identity

Establish the semantic foundation that drives all design decisions.

- Refer to [brand-identity.md](references/brand-identity.md) → Semantic Elements

Define:
- 3-5 brand personality traits with boundaries
- Voice & tone dimensions and default/variation rules
- Values and mission
- Relationship archetype with target users

#### 2. Map brand to perceptual elements

Translate semantic traits into perceptual design directions.

- Refer to [brand-identity.md](references/brand-identity.md) → Translation: Semantic to Perceptual

Create:
- Translation matrix (trait × perceptual element)
- Translation rules with rationale for key decisions
- Conflict resolution hierarchy

#### 3. Design token architecture

Convert perceptual directions into concrete token values.

- Refer to [design-system-methodology.md](references/design-system-methodology.md) → Token Architecture Design
- Refer to [styles.md](references/styles.md) → each section (token design phase only — do not carry M3 component names into later steps)

Steps:
1. Define Reference Tokens as raw values (color palette, font sizes, etc.)
2. Map to System Tokens with semantic names (primary, surface, etc.)
3. Define light and dark themes
4. Define typography scale (5 roles × 3 sizes)
5. Define shape scale (corner radius steps)
6. Define spacing scale (4dp base)
7. Define motion tokens (duration, easing)
8. Document semantic rationale for each System Token

#### 4. Design component patterns

Discover UI patterns from project requirements. **Do NOT start from an existing component catalog (e.g., Material's component library). Derive patterns from the project's user flows.**

- Refer to [design-system-methodology.md](references/design-system-methodology.md) → Component Design Principles
- Refer to [foundations.md](references/foundations.md)
- Do NOT refer to [styles.md](references/styles.md) during component design — styles.md is for token design only

##### Component Discovery Process

1. List the project's primary user flows
2. For each flow, identify what UI **roles** are needed (e.g., "prompt the user to take the next step", "let the user filter a dataset", "show a summary of an item")
3. Consolidate identical roles across flows into a single pattern
4. Name each pattern after its project-specific role, not after a standard UI library name (e.g., "recipe-card" instead of "card", "search-trigger" instead of "FAB")

##### For each pattern, define

- Role and emphasis level
- State system (default, hover, focused, pressed, disabled)
- Container structure (background, boundary, padding, content slots)
- Responsive adaptation rules
- Accessibility requirements
- Token mapping with rationale

#### 5. Organize patterns

Categorize patterns and define relationships.

- Refer to [design-system-methodology.md](references/design-system-methodology.md) → Pattern Organization

#### 6. Consider expressive expression (optional)

Apply when brand expression or engagement is important.

- Refer to [m3-expressive.md](references/m3-expressive.md)
- Follow the 80/20 rule (80% standard, 20% expressive)

#### 7. Generate output

Produce the project-level Agent Skill (`.claude/skills/design-system/SKILL.md`). Implement tokens and components directly in the project's codebase. See [Output Format](#output-format) below.

### Workflow 2: Extract Design System from Existing Product

When retroactively building a design system from a running product.

#### 1. Conduct UI inventory

- Collect screenshots
- List all color, font size, corner radius, and spacing values in use
- Identify inconsistencies and duplicates

#### 2. Infer brand identity

Reverse-engineer brand identity from existing design decisions.

- Refer to [brand-identity.md](references/brand-identity.md) → Semantic Elements
- Identify implicit personality traits from current design
- Document voice & tone patterns in existing copy
- Define the relationship archetype the current UI suggests
- Document gaps where semantic intent is unclear

#### 3. Standardize tokens

Organize scattered values into a token system with semantic rationale.

- Refer to [design-system-methodology.md](references/design-system-methodology.md) → Token Architecture Design
- Consolidate similar values and fit into a scale
- Assign semantic names
- Document rationale connecting tokens to inferred brand identity

#### 4. Extract patterns

- Refer to [design-system-methodology.md](references/design-system-methodology.md) → Pattern Organization
- Consolidate UI elements with the same role
- Organize variations
- Unify naming conventions

#### 5. Audit accessibility

- Refer to [foundations.md](references/foundations.md) → Accessibility
- Check color contrast, touch targets, keyboard operation, screen reader support

#### 6. Generate output

Produce the project-level Agent Skill (`.claude/skills/design-system/SKILL.md`). Implement tokens and components directly in the project's codebase. See [Output Format](#output-format) below.

### Workflow 3: Extend a Design System

When adding new patterns or tokens to an existing design system.

#### 1. Confirm necessity

- Will it be used in 3 or more screens? (reusability)
- Can existing patterns substitute?

#### 2. Design based on principles

- Refer to [design-system-methodology.md](references/design-system-methodology.md) → Component Design Principles
- Design from 5 perspectives: role, state, container, responsive, accessibility

#### 3. Define token mapping

- Check if existing System Tokens can express the need
- If new tokens are required, add following naming conventions
- Document semantic rationale for new tokens

#### 4. Update documentation

- Update the Agent Skill only if new design concepts or principles are introduced
- Implement tokens and components directly in the codebase (SSoT)
- Specify pattern relationships (alternative, compositional, exclusive)

### Workflow 4: Conduct a Design Review

When evaluating UI quality from a design system perspective.

#### 1. Check brand alignment

- Do design decisions reflect documented brand personality?
- Is the translation matrix being followed?
- Are there perceptual elements without semantic rationale?

#### 2. Check token compliance

- Are all colors, font sizes, corner radii, and spacing following tokens?
- Are there hard-coded values?

#### 3. Check pattern compliance

- Are defined patterns used correctly?
- Are undefined patterns proliferating?

#### 4. Check principle compliance

- Is visual hierarchy clear?
- Is the state system complete?
- Are accessibility requirements met?
- Is responsive adaptation in place?

#### 5. Check consistency

- Do components with the same role share unified expression?
- Is token usage consistent?

## Output Format

This skill produces a project-level Agent Skill. Token definitions and component implementations in the codebase are the Single Source of Truth (SSoT) — the generated skill documents brand identity, design principles, and translation rationale, then references the implementation.

### File Structure

```
.claude/skills/design-system/
└── SKILL.md              # Design system Agent Skill (auto-loaded by Claude)
```

Token definitions and components live in the project's own codebase (CSS variables, Tailwind config, theme files, component source, etc.). The Agent Skill references them but does not duplicate them.

### Generated SKILL.md

The generated `SKILL.md` is an Agent Skill with proper frontmatter. Claude auto-loads it when working on the project, applying the design system as context.

```markdown
---
name: design-system
description: "[Project name] design system. Defines brand identity, design principles, and translation rationale. Use when building UI, reviewing designs, or making visual decisions."
user-invocable: false
---

# [Project Name] Design System

## Brand Identity

### Personality
- [Trait 1]: [definition] / Not: [boundary]
- [Trait 2]: [definition] / Not: [boundary]
- [Trait 3]: [definition] / Not: [boundary]

### Voice & Tone
- Default: [description]
- Error: [variation]
- Success: [variation]
- Onboarding: [variation]

### Relationship: [archetype]
[Description of product-user dynamic]

## Translation Matrix

| Trait | Color | Typography | Shape | Spacing | Motion |
|-------|-------|------------|-------|---------|--------|
| ... | ... | ... | ... | ... | ... |

## Token Architecture

Implementation is SSoT. Refer to:
- [path to token implementation file(s)]

### Key Translation Rules
For each system-level token, document the semantic rationale:

- `--color-primary`: [trait] — [rationale]
- `--shape-medium`: [trait] — [rationale]
- ...

These rules apply when introducing new tokens or reviewing existing ones.
Existing implementation takes precedence unless it contradicts a principle documented here.

## Patterns

### Pattern Catalog
[Pattern names, roles, and relationships (alternative, compositional, exclusive)]

### Principles for New Patterns
When adding new patterns, follow:
- Role-based design (define by role, not appearance)
- Explicit state system (default, hover, focused, pressed, disabled)
- Container thinking (background, boundary, padding, content slots)
- Responsive adaptation (reflow, resize, show/hide, transform)
- Accessibility first (48×48dp touch target, WCAG AA contrast, keyboard, screen reader)

Implementation in the codebase is SSoT for existing patterns.
This section governs decisions when creating or modifying patterns.
```

### SSoT Principle

- Token values and component implementations in the codebase are always authoritative
- The Agent Skill documents **why** (brand identity, translation rationale, design principles), not **what** (concrete values)
- The Agent Skill is updated only when introducing new design concepts, correcting principle violations, or revising brand identity

## Decision Guide

| Situation | Reference to consult |
|-----------|---------------------|
| Define brand identity | [brand-identity.md](references/brand-identity.md) |
| Translate brand to design | [brand-identity.md](references/brand-identity.md) → Translation |
| Build a new design system | All references in workflow order |
| Design tokens | [design-system-methodology.md](references/design-system-methodology.md) → Token Architecture + [styles.md](references/styles.md) |
| Design components | [design-system-methodology.md](references/design-system-methodology.md) → Component Design Principles |
| Design layout | [foundations.md](references/foundations.md) → Layout |
| Check accessibility | [foundations.md](references/foundations.md) → Accessibility |
| Enrich expression | [m3-expressive.md](references/m3-expressive.md) |
| Audit existing UI | [design-system-methodology.md](references/design-system-methodology.md) → Governance + [brand-identity.md](references/brand-identity.md) |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/kurogoma4d) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
