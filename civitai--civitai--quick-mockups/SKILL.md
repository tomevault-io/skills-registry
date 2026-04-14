---
name: quick-mockups
description: Create multiple UI design mockups in parallel. Use when asked to create mockups, wireframes, or design variations for a feature. Creates HTML files using Mantine v7 + Tailwind following Civitai's design system. Use when this capability is needed.
metadata:
  author: civitai
---

# Quick Mockups

Create multiple design mockups in parallel using the `design-mockup` agent.

## Usage

When asked to create mockups for a feature:

1. **Create the output directory** if it doesn't exist:
   ```
   docs/working/mockups/<feature-name>/
   ```

2. **Launch 3-5 parallel mockup agents** using the Task tool with `subagent_type: design-mockup`

3. **Each agent creates a unique variation** with different:
   - Layout approaches (grid, list, masonry, cards)
   - Information hierarchy
   - Visual emphasis
   - Interaction patterns

## Directory Structure

```
docs/working/mockups/
├── crucible-discovery/
│   ├── v1-grid-cards.html
│   ├── v2-featured-hero.html
│   ├── v3-compact-list.html
│   └── v4-masonry.html
├── crucible-rating/
│   ├── v1-side-by-side.html
│   ├── v2-stacked.html
│   └── v3-swipe.html
└── [feature-name]/
    └── [variation].html
```

## Prompting Agents

When launching mockup agents, provide:
1. **Feature name** - What page/component to design
2. **Key requirements** - What must be included
3. **Variation focus** - What makes this variation unique
4. **Reference context** - Link to existing similar pages if helpful

Example prompt for agent:
```
Create a mockup for the Crucible Discovery page.

Requirements:
- List of active crucibles as cards
- Show: name, prize pool, time remaining, entry count
- Filter/sort controls (by prize, ending soon, newest)
- "Create Crucible" button

Variation: Grid layout with large hero card for featured crucible

Output to: docs/working/mockups/crucible-discovery/v1-featured-hero.html
```

## After Creating Mockups

1. List all created mockup files
2. Summarize each variation's approach
3. Ask user which direction to pursue or if they want more variations

## Tips

- Create variations that are meaningfully different, not just minor tweaks
- Consider mobile layouts in at least one variation
- Show realistic content (names, numbers, times)
- Include empty states where relevant
- Use the Civitai design patterns from `.claude/agents/design-mockup.md`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/civitai) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
