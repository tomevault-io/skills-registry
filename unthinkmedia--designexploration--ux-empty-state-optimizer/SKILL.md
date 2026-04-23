---
name: ux-empty-state-optimizer
description: Design effective empty states that guide, educate, and delight users. Use when reviewing interfaces with no data, improving first-run experiences, reducing user abandonment, or when user mentions "empty state", "no data", "first run", "onboarding", "blank screen", or "nothing to show". Use when this capability is needed.
metadata:
  author: unthinkmedia
---

# UX Empty State Optimizer

Transform empty screens from dead ends into opportunities.

## Empty State Types

### 1. First-Use Empty
User is new, hasn't created content yet.

**Goal:** Guide to first action, educate on value

### 2. No Results Empty
Search or filter returned nothing.

**Goal:** Help adjust search, suggest alternatives

### 3. Cleared Empty
User deleted all items, inbox zero.

**Goal:** Celebrate completion, suggest next steps

### 4. Error Empty
Data failed to load.

**Goal:** Explain problem, provide recovery path

### 5. Permission Empty
User lacks access to see content.

**Goal:** Explain why, offer path to access

## Anatomy of Good Empty States

### Essential Elements

1. **Visual** (optional but recommended)
   - Illustration, icon, or image
   - Sets tone (friendly, professional, etc.)

2. **Title**
   - Clear, friendly headline
   - Names what's missing or explains the state

3. **Description**
   - Brief explanation
   - Value proposition (first-use) or guidance (no results)

4. **Call to Action**
   - Primary action button
   - Optional secondary actions

### Optional Elements

5. **Help/Learn More**
   - Link to documentation
   - Quick tips

6. **Examples/Templates**
   - Pre-made content to start from
   - Reduces barrier to entry

## Evaluation Checklist

Score each empty state:

| Criterion | Points | Notes |
|-----------|--------|-------|
| Clear explanation of the state | 0-2 | |
| Actionable next step | 0-2 | |
| Appropriate visual tone | 0-1 | |
| Guidance for new users | 0-2 | |
| Path to resolve (if error) | 0-2 | |
| Delight/personality | 0-1 | |
| **Total** | **/10** | |

**Scores:**
- 8-10: Excellent
- 5-7: Adequate
- <5: Needs improvement

## Patterns by Type

### First-Use Empty

**Pattern: Educational Onboarding**

    [Illustration]
    
    Create your first API
    
    APIs let you expose your services to developers.
    Start by defining endpoints and methods.
    
    [ + Create API ]  [Learn more]

**Pattern: Template Start**

    [Illustration]
    
    No projects yet
    
    Start with a template or create from scratch.
    
    [ Browse templates ]  [ Create blank ]

**Pattern: Import Existing**

    [Illustration]
    
    Welcome! Let's get started
    
    Import existing work or start fresh.
    
    [ Import from file ]  [ Create new ]

### No Results Empty

**Pattern: Adjust Search**

    [Search icon]
    
    No APIs match "xzy"
    
    Try adjusting your search or filters.
    
    [Clear filters]  [Browse all APIs]

**Pattern: Create New**

    [Empty list icon]
    
    No items match your criteria
    
    Create one that does.
    
    [ Create new item ]

### Cleared/Complete Empty

**Pattern: Celebration**

    [Checkmark illustration]
    
    All done!
    
    You've completed all pending items.
    Nice work!
    
    [Back to dashboard]

**Pattern: What's Next**

    [Inbox zero illustration]
    
    Nothing pending
    
    Take a break, or explore what else you can do.
    
    [Explore features]  [View archive]

### Error Empty

**Pattern: Explain & Retry**

    [Warning icon]
    
    Couldn't load your data
    
    Check your connection and try again.
    
    [ Retry ]  [Contact support]

**Pattern: Detailed Error**

    [Error icon]
    
    Something went wrong
    
    Error: Connection timed out (Code: 408)
    This might be a temporary issue.
    
    [ Retry ]  [View status page]

### Permission Empty

**Pattern: Explain Access**

    [Lock icon]
    
    You don't have access
    
    Contact your admin to request access
    to this workspace.
    
    [ Request access ]

## Anti-Patterns to Fix

| Anti-Pattern | Problem | Fix |
|--------------|---------|-----|
| "No data" only | No guidance | Add explanation + CTA |
| No visual feedback | Looks broken | Add icon/illustration |
| Wrong tone | Error for first-use | Match state type |
| Dead end | No action possible | Always provide next step |
| Technical message | "null response" | Human-readable message |
| Hidden create button | CTA in header only | Add inline CTA |

## Output Format

    # Empty State Review: [Screen Name]

    ## Current State
    - Type: [First-use / No results / Cleared / Error / Permission]
    - Current content: [What it shows now]
    - Score: X/10

    ## Issues
    | Issue | Impact |
    |-------|--------|
    | [Problem] | [User consequence] |

    ## Recommended Design

    **Visual:** [Description of illustration/icon]
    
    **Title:** "[Headline text]"
    
    **Description:** "[Explanatory text]"
    
    **Primary CTA:** [ Button text ]
    
    **Secondary:** [Link text] (optional)

    ## Implementation Example
    
        [Icon/Illustration description]
        
        [Title]
        
        [Description text]
        
        [ Primary Action ]  [Secondary link]

## Writing Guidelines

### Do:
- Use friendly, human language
- Be specific about what's empty
- Provide clear, single next step
- Match the user's emotional state

### Don't:
- Use technical terms ("null", "empty array")
- Be condescending ("Oops!")
- Leave users stranded
- Over-explain

### Tone by Context

| Context | Tone | Example |
|---------|------|---------|
| First-use | Welcoming | "Welcome! Let's get you started" |
| No results | Helpful | "No matches found. Try adjusting filters" |
| Cleared | Congratulatory | "All caught up!" |
| Error | Reassuring | "Having trouble loading. Let's try again" |
| Permission | Informative | "This area requires admin access" |

## References

For empty state examples and templates: See [references/empty-state-examples.md](references/empty-state-examples.md)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/unthinkmedia) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
