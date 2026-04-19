---
name: refactoring-ui
description: Apply Refactoring UI principles to review and implement user interfaces. Use this skill when: (1) Reviewing UI components, designs, or screenshots for visual hierarchy, spacing, typography, color usage, or general improvements, (2) Implementing UI features with React and Tailwind CSS following modern design best practices, (3) Creating design systems or component libraries, (4) Improving existing UI code for better visual quality. Automatically applies when working with UI/frontend code or design feedback, even without explicit mention of Refactoring UI. Use when this capability is needed.
metadata:
  author: ykino1992
---

# Refactoring UI

Apply principles from "Refactoring UI" by Adam Wathan & Steve Schoger to create and review user interfaces with excellent visual design.

## Core Workflow

### When Reviewing UI

1. **Identify the context**: Component code, screenshot, design mockup, or live UI
2. **Load relevant references**: Based on what needs review (hierarchy, spacing, color, etc.)
3. **Apply principles**: Check against Refactoring UI guidelines
4. **Provide specific feedback**: Concrete suggestions with before/after examples when possible

**Common review areas:**
- Visual hierarchy (element importance, contrast, emphasis)
- Spacing and layout (whitespace, alignment, spacing systems)
- Typography (scale, line-height, readability)
- Color usage (palette, contrast, accessibility)
- Overall polish (borders, shadows, depth, finishing touches)

### When Implementing UI

1. **Understand requirements**: Feature description, design reference, or user request
2. **Apply design principles proactively**:
   - Start with feature content, not layout shell
   - Establish visual hierarchy through size, weight, and color
   - Use consistent spacing scale (e.g., 4px, 8px, 12px, 16px, 24px, 32px, 48px, 64px)
   - Implement proper typography scale
   - Create accessible color systems with HSL
3. **Write React + Tailwind CSS code**: Production-quality components
4. **Consider edge cases**: Empty states, long content, responsive behavior

## Technology Stack

Primary: **React** with **Tailwind CSS**

Example component structure:
```jsx
export function Card({ title, description, status }) {
  return (
    <div className="bg-white rounded-lg shadow-sm p-6 space-y-4">
      <div className="flex items-center justify-between">
        <h3 className="text-lg font-semibold text-gray-900">{title}</h3>
        <span className="text-sm font-medium text-blue-600">{status}</span>
      </div>
      <p className="text-sm text-gray-600 leading-relaxed">{description}</p>
    </div>
  )
}
```

## Key Principles Summary

**Visual Hierarchy:**
- Not all elements are equal - size isn't everything
- Use weight and color, not just size
- De-emphasize to emphasize important elements
- Labels are often unnecessary - use natural hierarchy

**Spacing & Layout:**
- Start with too much whitespace, then reduce
- Use a consistent spacing scale
- Relative sizing doesn't scale well - define specific sizes
- Avoid ambiguous spacing between elements

**Typography:**
- Establish a type scale (e.g., 12px, 14px, 16px, 18px, 20px, 24px, 30px, 36px, 48px)
- Line-height is proportional (tight for headings, loose for body)
- Align text with readability in mind
- Not every link needs color

**Color:**
- Use HSL over hex for better control
- Need 5-10 shades per color (not just 1-2)
- Define shades upfront (don't generate on the fly)
- Greys don't have to be grey (add subtle color)
- Accessible doesn't have to mean ugly

**Depth & Polish:**
- Use shadows to convey elevation
- Shadows can have two parts (close + distant)
- Add color with accent borders
- Use fewer borders - try spacing, shadows, or backgrounds instead
- Don't overlook empty states

## References

This skill includes extracted chapters from Refactoring UI in the **`references/`** directory.

Reference files are located at: `~/.claude/skills/refactoring-ui/references/`

Load these as needed based on the specific area of focus:

- **references/hierarchy_is_everything.txt** - Visual hierarchy, emphasis, contrast
- **references/layout_and_spacing.txt** - Whitespace, spacing systems, layouts
- **references/designing_text.txt** - Typography, scales, line-height, readability
- **references/working_with_color.txt** - HSL, color palettes, accessibility
- **references/creating_depth.txt** - Shadows, elevation, layers
- **references/starting_from_scratch.txt** - Feature-first design, personality, constraints
- **references/working_with_images.txt** - Photos, contrast, user content
- **references/finishing_touches.txt** - Borders, backgrounds, empty states, polish

When reviewing or implementing UI, read the most relevant references for the specific area you're focusing on.

## Review Process

**For component code:**
1. Read the code to understand structure and styling
2. Load relevant reference sections
3. Identify issues and suggest improvements
4. Provide updated code with explanations

**For screenshots/images:**
1. Analyze the visual design
2. Load relevant reference sections
3. Point out specific issues with visual markers if possible
4. Suggest concrete improvements

## Implementation Process

**For new features:**
1. Clarify requirements if needed
2. Plan component hierarchy and spacing
3. Load relevant references (spacing, color, typography as needed)
4. Write React + Tailwind CSS code following principles
5. Consider responsive behavior and edge cases

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ykino1992) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
