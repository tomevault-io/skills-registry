---
name: ux-annotation-generator
description: Generate detailed annotations for UI screenshots identifying pain points, strengths, accessibility issues, and improvement suggestions with precise location markers. Use when creating visual design feedback, annotating mockups for developers, documenting UX issues for stakeholders, or when user mentions "annotate", "mark up", "visual feedback", "screenshot review", or "design annotations". Use when this capability is needed.
metadata:
  author: unthinkmedia
---

# UX Annotation Generator

Create structured annotations for UI screenshots with precise location references.

## Annotation Types

### 1. Issue Annotations (Problems)
Mark usability problems, accessibility issues, inconsistencies.

**Severity levels:**
- 🔴 Critical - Blocks task completion
- 🟠 Major - Significant friction
- 🟡 Minor - Polish opportunity
- 🔵 Info - Observation, not necessarily a problem

### 2. Strength Annotations (What's Working)
Highlight effective patterns worth preserving or replicating.

**Categories:**
- ✅ Good pattern
- ⭐ Excellent implementation
- 💡 Innovative solution

### 3. Suggestion Annotations (Improvements)
Propose specific changes with rationale.

**Types:**
- 🔧 Quick fix - Easy change, big impact
- 🏗️ Structural - Requires design rework
- 🎨 Visual - Aesthetic improvement
- ♿ Accessibility - A11y enhancement

## Location Reference System

Use consistent location descriptors:

### Grid-Based
```
Top-left | Top-center | Top-right
─────────┼────────────┼──────────
Mid-left | Center     | Mid-right
─────────┼────────────┼──────────
Bot-left | Bot-center | Bot-right
```

### Component-Based
Reference by UI element:
- "Header > Navigation > Third menu item"
- "Sidebar > APIs section > TestAPI row"
- "Main content > Form > Email field"

### Coordinate Hints
For precise marking:
- "Approximately 200px from left, 150px from top"
- "Bottom third of the screen, right side"

## Annotation Format

### Single Annotation

```
[#1] 🔴 CRITICAL | Location: [Precise location]
─────────────────────────────────────────────
Issue: [What's wrong]
Impact: [How it affects users]
Recommendation: [Specific fix]
Related: [Link to heuristic/principle if applicable]
```

### Annotation Set

```
# UI Annotations: [Screen Name]

## Screenshot Reference
[Description of what the screenshot shows]

## Annotations

### Issues

[#1] 🔴 CRITICAL | Top-right, user menu area
Issue: No visible logout option
Impact: Users cannot securely end session
Recommendation: Add "Sign out" to user dropdown menu
Related: H3 (User control and freedom)

[#2] 🟠 MAJOR | Sidebar, bottom section
Issue: 15+ navigation items without grouping
Impact: Users struggle to scan and find items
Recommendation: Group into collapsible categories
Related: Cognitive load - Miller's Law

[#3] 🟡 MINOR | Main content, table headers
Issue: Headers not visually distinct from data rows
Impact: Slight scanning difficulty
Recommendation: Add background color or bold weight to headers

### Strengths

[#4] ✅ GOOD | Center, policy editor
Strength: Clear visual flow showing request → processing → response
Why it works: Matches mental model of API request lifecycle
Preserve: This pattern for similar flow visualizations

### Suggestions

[#5] 🔧 QUICK FIX | Top-center, breadcrumb
Suggestion: Add "copy path" button to breadcrumb
Rationale: Power users often need to reference/share locations
Effort: Low | Impact: Medium

## Summary
- Critical issues: 1
- Major issues: 1
- Minor issues: 1
- Strengths noted: 1
- Suggestions: 1
```

## Visual Annotation Symbols

When describing annotations for visual overlay:

### Shapes and Colors

| Type | Shape | Color |
|------|-------|-------|
| Critical issue | Circle with X | Red |
| Major issue | Circle with ! | Orange |
| Minor issue | Circle with dot | Yellow |
| Strength | Checkmark | Green |
| Suggestion | Lightbulb | Blue |
| Info/Note | "i" icon | Gray |

### Callout Style

```
       ┌─────────────────────────────────┐
  [#1] │ Issue: Missing confirmation     │
   🔴  │ Fix: Add toast notification     │
       └─────────────────────────────────┘
           │
           ▼
    [UI Element being annotated]
```

## Multi-Screen Annotation

For flows spanning multiple screens:

```
# Flow Annotations: [Flow Name]

## Screen 1: [Name]
[Annotations for screen 1]

─── User Action: [Click "Next"] ───

## Screen 2: [Name]
[Annotations for screen 2]

─── User Action: [Fill form and submit] ───

## Screen 3: [Name]
[Annotations for screen 3]

## Cross-Screen Issues

[#CS1] Flow continuity
Screens 1→2: Context lost - user must remember API name
Recommendation: Persist API name in header across all screens
```

## Annotation Checklist

For each screen, check:

- [ ] Primary action clarity (is main CTA obvious?)
- [ ] Navigation orientation (does user know where they are?)
- [ ] Error states (how would errors appear?)
- [ ] Empty states (what if no data?)
- [ ] Loading states (what during async operations?)
- [ ] Accessibility basics (contrast, labels, focus)
- [ ] Mobile/responsive (if applicable)

## Output for Different Audiences

### For Designers
Include: Visual details, spacing notes, component suggestions, design system alignment

### For Developers
Include: Component names, interaction behavior, state handling, accessibility requirements

### For Stakeholders
Include: User impact, business implications, priority recommendations, effort estimates

## References

For annotation templates and examples: See [references/annotation-templates.md](references/annotation-templates.md)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/unthinkmedia) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
