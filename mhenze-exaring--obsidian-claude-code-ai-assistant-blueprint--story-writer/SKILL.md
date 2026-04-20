---
name: story-writer
description: Creates and edits development stories in standard format. Activates on "Create story for...", "Story for...", "Revise story..." or similar requests.
metadata:
  author: mhenze-exaring
---

# Story Writer Skill

Creates professional development stories in standardized format for issue trackers, based on unstructured requirements.

## When to Use This Skill

Activate on phrases like:
- "Create (me) a story for..."
- "Story for..."
- "Write a story..."
- "Revise the story..."
- "Improve the current story..."
- "Change the story as follows..."
- "New story: ..."

## Story Format (Template)

```markdown
# [Short Title] \[Team-Tag]

**As [Role], I want [Goal/Desire], so that [Benefit/Reason]**

Context: [Detailed description of the background, current state, problem, and goal of the story. Explains the "why" and provides developers with the necessary context.]

**AC**
- [Acceptance criterion 1 - concrete, testable, from user perspective]
- [Acceptance criterion 2]
- Nested details as sub-bullets:
    - Detail A
    - Detail B
- [Additional criteria...]
```

## Format Rules

> **CRITICAL: Output language is ALWAYS English.**
> Even if the user provides input in another language, ALL output (title, user story, context, ACs) MUST be in English.
> Translate and adapt - do not copy non-English text.

### Language
- **ALWAYS English** - regardless of input language
- Translate requirements to English
- Use clear, professional technical English
- Avoid jargon unless domain-specific

### Title
- Short and concise (3-6 words)
- Team tag in square brackets at the end: `[TEAM]`
- Default team tag: Configure in CLAUDE.md

### User Story Statement
- Format: `**As [Role], I want [Goal], so that [Benefit]**`
- Bold formatted
- Role: Product-Management, Customer, Support-Agent, Developer, etc.
- Goal: What should be achieved (active voice)
- Benefit: Why this matters (business value)

### Context
- Starts with "Context:" (not bold)
- Explains background and current state
- Describes the problem or motivation
- Provides technical context when relevant
- 3-8 sentences, cohesive paragraph

### Acceptance Criteria (AC)

> **Writing Style:** ACs describe the **desired end state after implementation**.
> Use present tense to describe what **exists/works** when the story is done.
> Think: "When this story is complete, these things are true."

- Starts with `**AC**` (bold, no colon)
- Bullet list with `-`
- Each criterion:
  - **Present tense** describing the post-implementation state
  - Concrete, specific, and testable (pass/fail)
  - Focuses on **user experience/outcome**, not implementation details
  - Independent of other criteria
- Sub-bullets for details with 4 spaces indentation
- Optional: `(Optional)` prefix for nice-to-have criteria

**AC Writing Patterns (use these):**
- "There is a [element] in/on [location]" ✓
- "The [feature] displays/shows [content]" ✓
- "Users can [action]" ✓
- "[Action] results in [outcome]" ✓
- "The system [behavior] when [condition]" ✓

**Avoid these patterns:**
- "The developer should implement..." ✗
- "Add a button that..." ✗
- "We need to..." ✗
- Future tense ("will be", "shall have") ✗

## Workflow

### Create New Story

1. **Analyze input:**
   - Identify main goal
   - Determine stakeholder/role
   - Extract business value
   - Collect technical details
   - **Team tag:** Use default if not specified

2. **Search vault for context:**
   - Use `mcp__obsidian__search_vault_simple` with relevant keywords from input
   - Search for:
     - Existing stories on the same topic area
     - Technical documentation
     - Meeting notes with relevant decisions
   - Read found relevant files with `mcp__obsidian__get_vault_file`
   - **Integrate found context:**
     - Technical details in Context paragraph
     - Known constraints as ACs
     - References to related systems/processes

3. **Structure story:**
   - Formulate appropriate title (English)
   - Write user story statement
   - Draft context paragraph (enriched with vault context)
   - Derive ACs from requirements + vault findings

4. **Create file:**
   - Filename: `Story - [Title].md`
   - Location: vault root `/` (inbox)
   - Frontmatter with created/updated

5. **Offer review:**
   - Present story
   - Mention found context sources
   - Ask for feedback
   - Iteratively improve

### Revise Story

1. **Load active file or named story**
2. **Incorporate change requests**
3. **Update file**
4. **Summarize changes**

## Example Transformation

### Input (unstructured):
> "We need a way to manage the product order in the Product Pilot. Currently it's hardcoded and every change requires a deployment. It should work via drag & drop and aliases should also be manageable."

### Output (structured English story):
```markdown
# Product Lineup Management \[TEAM]

**As Product-Management, I want to manage the order and aliases of products in a back-office UI, so that lineup changes no longer require code deployments**

Context: Currently, the product lineup order is hardcoded in the service repository, and alias management requires manual intervention across multiple systems. Every change to the product order or alias configuration requires a ticket, development resources, and a deployment cycle. This creates unnecessary overhead and delays for simple lineup adjustments. Moving this functionality to the back-office will enable self-service management of the product lineup.

**AC**
- There is a "Lineup" navigation item in the menu
- The Lineup page displays all products ordered by their current rank
- Products can be reordered via drag-and-drop
- Each product row shows:
    - The main alias name
    - The linked product name and price
    - The product ID
- Reordering persists the new rank immediately on drop
- Main aliases can be reassigned to different products
```

### AC Pattern Comparison

| Avoid (imperative/future) | Use (present tense, end state) |
|---------------------------|--------------------------------|
| "Add a search field" | "There is a search field on the page" |
| "The system will validate input" | "The system validates input on submit" |
| "Users should be able to filter" | "Users can filter results by date" |
| "Implement error handling" | "Invalid input displays an error message" |
| "Create an export function" | "Data can be exported as CSV or PDF" |

## File Operations

### New Story
```
Location: /Story - [Title].md  (vault root = inbox)
```

### Edit Story
- Use `mcp__obsidian__get_active_file` when user references "current story"
- Or search for filename in vault

## Interactive Mode

After creating a story:
1. Present the generated story
2. Ask: "Should I adjust anything? (e.g., add ACs, expand context, change title)"
3. Iterate based on feedback

## Issue Tracker Copy-Paste Note

The story is formatted for direct paste into issue tracker Description field:
- Markdown syntax is interpreted
- Bullet lists are preserved
- Bold formatting works

> **Tip:** Use "Text" mode (not Visual) in the issue tracker for best results when pasting.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mhenze-exaring) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
