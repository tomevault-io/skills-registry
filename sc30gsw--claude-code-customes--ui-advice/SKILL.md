---
name: ui-advice
description: Provides UI/UX design pattern advice and generates text wireframes
metadata:
  author: sc30gsw
---

# UI Advice

UI/UX design expert providing design pattern advice with text wireframes.

## What This Skill Provides

1. **Design Pattern Suggestions**: 3-5 design patterns suitable for requirements
2. **Pattern Explanations**: Features, pros/cons, and use cases
3. **Text Wireframes**: Simple ASCII wireframes for each pattern
4. **Recommendations**: Most suitable pattern for the situation

## Guidelines

- Reference patterns from famous design systems (Material Design, Apple HIG, Ant Design)
- Consider usability, accessibility, and current trends
- Create simple yet easy-to-understand wireframes
- Include implementation complexity for each pattern
- When using Mantine, retrieve latest component information via Context7

## Wireframe Rules

```
┌─────────────────────────┐
│ Header Title            │
├─────────────────────────┤
│ [ Button ] [ Button ]   │
│                         │
│ Content Area            │
│ ┌─────┐ ┌─────┐         │
│ │Card │ │Card │         │
│ └─────┘ └─────┘         │
└─────────────────────────┘
```

Use simple ASCII characters to clearly express the structure.

## Example Output

```
## Pattern 1: Card Grid Layout

### Description
Grid-based card layout optimized for displaying multiple items.

### Pros
- Scannable at a glance
- Responsive-friendly
- Good for visual content

### Cons
- Less information density
- May require scrolling

### Wireframe
┌─────────────────────────────────┐
│  🔍 Search    [Filter ▼]        │
├─────────────────────────────────┤
│  ┌────────┐  ┌────────┐         │
│  │  IMG   │  │  IMG   │         │
│  │  Title │  │  Title │         │
│  │  Desc  │  │  Desc  │         │
│  └────────┘  └────────┘         │
└─────────────────────────────────┘

### Implementation Complexity: Low
```

## Usage

Simply describe your UI/UX requirements and this skill will analyze and provide design pattern recommendations with wireframes.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/sc30gsw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
