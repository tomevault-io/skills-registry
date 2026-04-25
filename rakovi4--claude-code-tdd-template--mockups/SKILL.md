---
name: mockups
description: Generate HTML interface mockups for user stories. Use when user wants to create UI mockups, prototypes, or mentions /mockups command. Use when this capability is needed.
metadata:
  author: rakovi4
---

# Generate Interface Mockups

Create production-quality HTML mockups for a user story with mandatory desktop/mobile parity.

## Usage
```
/mockups "Story name"
/mockups 5                    # By MVP story number
/mockups                      # Interactive selection
```

## Phase 1: Context Gathering

Read these files before generating anything:

1. `ProductSpecification/BriefProductDescription.txt`
2. `ProductSpecification/MvpStories.txt` (story number lookup)
3. Story spec: `ProductSpecification/stories/NN-story-name/NN_StoryName.md`
4. Landing page: `landing-page/index.html` (design reference)
5. If exists: `ProductSpecification/stories/NN-story-name/story-specifics.txt`

## Phase 2: Story Selection

Parse user input:
- **By name**: `/mockups "Login"` - partial match against MvpStories.txt
- **By number**: `/mockups 5` - story #5 from MvpStories.txt
- **Interactive**: `/mockups` - list stories, ask user to choose

## Phase 3: Screen Plan (MANDATORY GATE)

You MUST complete this phase fully before writing any HTML file.

### Step 1: Extract all screen states

Read the story spec's "Screen States" section. Copy every numbered item into a list.

**MUST rules:**
- MUST include every state listed in the spec. NEVER skip, merge, or combine states.
- If the spec says "Login Form - server error" and "Login Form - validation errors", those are TWO separate files.
- If the spec marks a state as "(optional)", still include it.

### Step 2: Map states to filenames

Create the complete file plan for BOTH desktop AND mobile. Every desktop file MUST have a mobile counterpart. No exceptions.

Output the plan as a table before generating any HTML:

```
SCREEN PLAN (N desktop + N mobile = 2N total files):

| # | Screen State (from spec) | Desktop File | Mobile File |
|---|--------------------------|--------------|-------------|
| 1 | Login Form - empty       | desktop/01-login-form.html | mobile/01-login-form.html |
| 2 | Login Form - validation  | desktop/02-login-form-validation.html | mobile/02-login-form-validation.html |
...
```

### Step 3: Parity check

Count: desktop files = N, mobile files = N. If counts differ, fix the plan before proceeding.

**NEVER proceed to Phase 4 until the Screen Plan table is complete with equal desktop and mobile counts.**

## Phase 4: Generate Mockups

Generate every file listed in the Screen Plan. Check off each row as you create both its desktop AND mobile file.

### Output Location
```
ProductSpecification/stories/NN-story-name/mockups/
├── desktop/
│   ├── 01-screen-name.html
│   └── screenshots/
└── mobile/
    ├── 01-screen-name.html
    └── screenshots/
```

### Design System Tokens
```css
:root {
    --bg-primary: #fafbfc;
    --bg-secondary: #ffffff;
    --bg-tertiary: #f3f4f6;
    --accent: #10b981;
    --accent-light: #d1fae5;
    --accent-dark: #059669;
    --text-primary: #111827;
    --text-secondary: #4b5563;
    --text-muted: #9ca3af;
    --border: #e5e7eb;
    --warning: #f59e0b;
    --warning-light: #fef3c7;
    --error: #ef4444;
    --error-light: #fee2e2;
}
```

### Generation Rules

**Format:**
- Standalone HTML with embedded CSS. No external dependencies except Google Fonts (Inter).
- `lang="en"`. Interface text in English.
- Inline SVG for icons. Unsplash URLs for product images.
- One file per screen state. Naming: `NN-descriptive-name.html`.
- Desktop and mobile are separate files, NOT responsive breakpoints.

**Desktop (viewport 1400px):**
- Sidebar navigation visible
- Multi-column layouts where appropriate

**Mobile (viewport 375px):**
- Bottom sticky navigation
- Single-column layouts
- Touch targets min 44x44px

**Story-specifics:**
- If `story-specifics.txt` exists, apply its design notes
- Add HTML comments referencing external docs if applicable

### Completion Check

After generating all files, verify against the Screen Plan table:
- Every row has both desktop AND mobile files created
- File count matches plan exactly
- No spec screen state was skipped

If any file is missing, create it before proceeding.

## Phase 5: Screenshots

Run the screenshot skill for both directories:
```
/screenshot ProductSpecification/stories/NN-story-name/mockups/desktop/
/screenshot ProductSpecification/stories/NN-story-name/mockups/mobile/
```

## Phase 6: Summary

Report:
1. Screen Plan table (as generated in Phase 3)
2. Total files: N desktop + N mobile = 2N
3. Screenshot locations
4. Design decisions or notes

## Pre-flight Checklist

Before declaring done, confirm ALL of the following:
- [ ] Every screen state from the spec's "Screen States" section has a mockup
- [ ] Desktop file count equals mobile file count
- [ ] All files use the design system tokens above
- [ ] Interface text is in English
- [ ] Screenshots were generated for both desktop and mobile
- [ ] Existing mockups were checked (ask user before overwriting)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/rakovi4) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-12 -->
