---
name: swift-design-brief
description: Generate a Design Brief for Apple platform apps (iOS, iPadOS, macOS, watchOS) that serves as a long-lived, authoritative reference document for LLM-driven design decisions. Use when the user asks to "create a design brief", "define the design direction", "write a design brief for my app", or needs a stable reference that prevents design drift across multiple LLM sessions. The output is structured so another LLM can reliably parse and apply it. Assumes SwiftUI as the primary UI framework and Apple HIG as the baseline. Use when this capability is needed.
metadata:
  author: nhoogendoorn
---

# LLM Design Brief Generator — Apple Platforms

Generate opinionated, internally consistent Design Briefs for Apple platform apps. The brief captures design intent, encodes trade-offs, and provides decision heuristics — not feature lists. It acts as a stable reference document that future LLM sessions use to make aligned design decisions.

## Apple HIG Foundation

Every brief inherits these constraints from Apple's Human Interface Guidelines. Do not repeat them in the brief output — they are assumed. Only mention platform specifics when the product *deviates* from defaults or makes a deliberate choice between options.

**Core principles:** Clarity (content legible and focused), Deference (UI supports content, never competes), Depth (visual layers convey hierarchy).

**Typography:** San Francisco system font. Dynamic Type support required. Semantic styles: `.largeTitle` (34pt), `.title` (28pt), `.headline` (17pt semibold), `.body` (17pt), `.caption` (12pt). Minimum text: 11pt. Custom fonts must use `relativeTo:` for Dynamic Type scaling.

**Colors:** Semantic colors (`Color(.label)`, `Color(.systemBackground)`, etc.) adapt to light/dark mode automatically. System accent colors for interactive elements. Custom colors require both light and dark variants. WCAG AA contrast: 4.5:1 for text, 3:1 for large text and UI components.

**Spacing:** 8pt grid. Standard padding: 16pt. Touch targets: minimum 44x44pt.

**Icons:** SF Symbols (6000+). Use `.symbolRenderingMode(.hierarchical)` or `.multicolor` for semantic meaning. Custom icons only when SF Symbols lacks coverage.

**Navigation patterns:**
- **Tab bar** — 3-5 top-level destinations, bottom of screen, never hidden
- **NavigationStack** — Hierarchical drill-down, large titles for top-level, inline for detail
- **NavigationSplitView** — Sidebar + detail for iPad/macOS, collapses on iPhone
- **Sheets** — Modal content with `.presentationDetents([.medium, .large])`

**Accessibility:** VoiceOver labels on all interactive elements. Respect Reduce Motion, Increase Contrast, Bold Text. Never rely on color alone for meaning.

**Platform differences:**
- **iPhone** — Portrait-first, one-handed use, safe areas for notch/Dynamic Island
- **iPad** — Sidebar navigation, multitasking (Split View), keyboard shortcuts, pointer support
- **macOS** — Menu bar, window management, keyboard-first interaction, higher information density
- **watchOS** — Glanceable, minimal interaction, Digital Crown scrolling, large touch targets

## Workflow

### 1. Gather Input

Ask for the required field. Infer sensible defaults for optional fields and state assumptions explicitly. Actively encourage the user to share design inspiration — existing apps, screenshots, or references that capture the feel they're after. Inspiration produces significantly better briefs.

**Required:**
- Product description

**Optional (infer if not provided):**
- Target platform(s) — defaults to iOS if unspecified
- Target audience
- Brand personality keywords
- Hard constraints
- Design inspiration — screenshots, app names, or URLs of existing apps whose look, feel, or interaction patterns the user wants to draw from. Use these as directional reference, not as targets to copy. Extract the relevant qualities (e.g., "the information density of Things 3" or "the playful motion of Duolingo") and fold them into the brief's visual direction and design principles.

Do not repeat user input verbatim — fold it into the brief's structure.

### 2. Generate the Brief

Follow the exact output structure in `references/output_template.md`. Every section is mandatory.

**Writing rules:**
- Use clear, assertive language
- Be opinionated — the brief must help an LLM say "no" to misaligned choices
- Avoid vague terms like "clean", "modern", or "intuitive" unless operationally defined (e.g., "intuitive = task completion without documentation")
- Prioritize internal consistency — principles, heuristics, and red flags must not contradict each other
- Keep it concise but unambiguous — aim for 400-800 words total
- Ground visual direction in Apple platform conventions from the section above
- Only specify Apple Platform Specifics when the product deviates from defaults or makes a deliberate choice (e.g., custom font, non-standard navigation)

### 3. Validate

Before delivering, verify:
- Decision Heuristics resolve common trade-offs (simplicity vs. power, consistency vs. novelty, platform convention vs. brand expression)
- Red Flags are specific enough to catch real mistakes on Apple platforms
- Assumptions are stated, not buried
- Design Principles are ordered by priority, not listed arbitrarily
- Visual direction aligns with the target Apple platform(s)

## How the Brief Gets Used

In subsequent sessions, the user (or another LLM) references the brief like this:

> "Use the following Design Brief as the authoritative source for all design decisions. If a decision is not explicitly covered, infer the choice that best aligns with the Design Principles and Decision Heuristics."

This prevents the model from drifting or inventing a new aesthetic every turn. The brief is the source of truth.

## Resources

- `references/output_template.md` — The exact output structure to follow

---
> Source: [nhoogendoorn/Skills](https://github.com/nhoogendoorn/Skills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
