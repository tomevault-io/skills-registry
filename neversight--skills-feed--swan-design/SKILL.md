---
name: swan-design
description: Craft elegant technical specifications with ASCII artistry, flow diagrams, and the Grove voice. The swan glides with purpose—vision first, then form, then perfection. Use when creating specs, reviewing documents, or transforming technical plans into storybook entries. Use when this capability is needed.
metadata:
  author: neversight
---

# Swan Design 🦢

The swan doesn't rush. It glides across still water with purpose and grace. Each movement is deliberate. Every feather is in place. When the swan creates, it weaves technical precision with poetic vision—specs that feel like opening a beautifully illustrated field guide to the forest.

## When to Activate

- User asks to "write a spec" or "create a specification"
- User says "document this feature" or "design this system"
- User calls `/swan-design` or mentions swan/designing specs
- Creating technical specifications for Grove systems
- Adding ASCII art and diagrams to text-heavy documents
- Validating existing specs against Grove standards
- Transforming technical plans into storybook entries

**Pair with:** `owl-archive` for Grove voice and text refinement

---

## The Design

```
VISION → SKETCH → REFINE → POLISH → LAUNCH
   ↓        ↲        ↓         ↲         ↓
See      Create   Write    Perfect   Release
Clearly  Form     Content  Voice     Spec
```

### Phase 1: VISION

*The swan sees the whole lake before moving a single feather...*

Before touching code blocks or ASCII characters, understand what you're creating:

**What is this system/feature?**
- What problem does it solve?
- What would you tell a Wanderer about it?
- What's the nature metaphor that fits?

**The nature metaphor is everything.** Examples from Grove:
- Heartwood → The center that holds (auth system)
- Wisp → Gentle guiding light (help system)
- Porch → Sit and chat about what's going on (support)
- Patina → Age as armor (backup system)

**Questions to answer:**
1. **Concept:** What natural thing does this resemble?
2. **Scope:** What's in/out of bounds for this spec?
3. **Audience:** Developers implementing? Wanderers exploring?
4. **Tone:** Technical precision, warm invitation, or both?

**Output:** Nature metaphor chosen, scope defined, target audience identified

---

### Phase 2: SKETCH

*The swan traces patterns on the water, creating the form...*

Build the spec structure with required elements:

**Frontmatter (REQUIRED):**

```yaml
---
aliases: []
date created: Monday, January 6th 2026
date modified: Monday, January 13th 2026
tags:
  - primary-domain
  - tech-stack
  - category
type: tech-spec
---
```

**Date format examples:**
- `Monday, December 29th 2025`
- `Saturday, January 4th 2026`

**Type options:**
- `tech-spec` — Technical specification (most common)
- `implementation-plan` — Step-by-step implementation guide
- `index` — Index/navigation document

**ASCII Art Header:**

Create visual representation of the concept using:
- Box-drawing characters: `─│┌┐└┘├┤┬┴┼╭╮╰╯`
- Nature emoji (sparingly): `🌲🌿🍂✨🌸`
- Under 20 lines, centered, with poetic tagline

**Examples from excellent specs:**

**Wisp (will-o'-the-wisp light):**
```
         🌲  🌲  🌲
          \   |   /
           \  |  /
             ✨
            ╱ ╲
           ╱   ╲
          ╱  ·  ╲
         ╱   ·   ╲
        ╱    ·    ╲
       ·     ·     ·
         gentle
         guiding
          light
```

**Patina (layered backups):**
```
                     ╭───────────────────╮
                    ╭┤  ┌─────────────┐  ├╮
                   ╭┤│  │  2026-01-05 │  │├╮
                   │││  │  ▓▓▓▓▓▓▓▓▓▓ │  │││
                   │││  │  ▒▒▒▒▒▒▒▒▒▒ │  │││
                   │││  │  ░░░░░░░░░░ │  │││
                   │││  │  ·········· │  │││
                   ╰┴┴──└─────────────┘──┴┴╯
                  ╱╱╱╱╱╱╱╱╱╱╱╱╱╱╱╱╱╱╱╱╱╱╱╱╱╱
               ──────────────────────────────
              ~~~~~~~~ oxidation layer ~~~~~~~~
              Age as armor. Time as protection.
```

**Heartwood (tree rings):**
```
                      ╭──────────╮
                   ╭──│ ╭──────╮ │──╮
                 ╭─│  │ │ ╭──╮ │ │  │─╮
                │  │  │ │ │♥ │ │ │  │  │
                 ╰─│  │ │ ╰──╯ │ │  │─╯
                   ╰──│ ╰──────╯ │──╯
                      ╰──────────╯

       every ring: a year, a story, a layer of growth

               The center that holds it all.
```

**Introduction Template:**

```markdown
> *Poetic tagline in italics*

[2-3 sentence description of what this is in the Grove ecosystem]

**Public Name:** [Name]
**Internal Name:** Grove[Name]
**Domain:** `name.grove.place`
**Repository:** [Link if applicable]
**Last Updated:** [Month Year]

[1-2 paragraphs explaining the nature metaphor and how it applies]

---
```

**Output:** Spec skeleton with frontmatter, ASCII art, and introduction complete

---

### Phase 3: REFINE

*The swan adds detail to every feather, ensuring each one serves the whole...*

Write the technical content with visual elements:

**Required Sections:**
- **Overview/Goals** — What this system does
- **Architecture** — How it's built (with diagrams!)
- **Tech Stack** — Dependencies, frameworks
- **API/Schema** — Technical details
- **Security** — Important considerations
- **Implementation Checklist** — Clear action items

**ASCII Flow Diagrams:**

Every process MUST include at least one flow diagram:

```
┌─────────────────────────────────────────────────────────────────────┐
│                         Client Sites                                │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐               │
│  │   Site A     │  │   Site B     │  │   Site C     │               │
│  └──────┬───────┘  └──────┬───────┘  └──────┬───────┘               │
└─────────┼─────────────────┼─────────────────┼───────────────────────┘
          │                 │                 │
          │    1. Request   │                 │
          ▼                 ▼                 ▼
┌─────────────────────────────────────────────────────────────────────┐
│                        Central Service                              │
│                                                                     │
│  ┌─────────────────────────┐  ┌─────────────────────────┐           │
│  │      Handler A          │  │      Handler B          │           │
│  └─────────────────────────┘  └─────────────────────────┘           │
└─────────────────────────────────────────────────────────────────────┘
```

**Box Drawing Reference:**
- Corners: `┌ ┐ └ ┘` (square) or `╭ ╮ ╰ ╯` (rounded)
- Lines: `─ │ ═ ║`
- Joins: `├ ┤ ┬ ┴ ┼`
- Arrows: `→ ← ↑ ↓ ▶ ◀ ▲ ▼`

**UI Mockups (if applicable):**

```
┌─────────────────────────────────────────────────────────────────┐
│  ✧ Panel Title                                          [×]      │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│  ┌─ Label ────────────────────────────────────────────────┐     │
│  │ Content here with proper spacing                       │     │
│  └────────────────────────────────────────────────────────┘     │
│                                                                 │
│  ┌─────────────────────────────────────────────────────────┐    │
│  │ Input field...                                     [↵]  │    │
│  └─────────────────────────────────────────────────────────┘    │
│                                                                 │
│  ───────────────────────────────────────────────────────────    │
│  [ Action A ]                              [ Action B ✦ ]       │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

**State Diagrams:**

For features with multiple states:

```
   Idle:                    Analyzing:               Success:
   .  *  .    .  *         . * . analyzing . *           *
  .    _    .      .         \  |  /             .    *  /|\   .
     /   \    *  .         -- (o.o) --  thinking    *   / | \    *
    / ~ ~ \  .    .          /  |  \                   /__|__\
   /       \______        ~~~~~~~~~~~~~~~~~       ~~~~/       \~~~~
  ~~~~~~~~~~~~~~~~~~~       words flowing...        all clear
```

**Comparison Tables:**

```markdown
| Feature | Seedling | Sapling | Oak | Evergreen |
|---------|----------|---------|-----|-----------|
| Posts   | 50       | 250     | ∞   | ∞         |
| Storage | 1 GB     | 5 GB    | 20 GB | 100 GB  |
| Themes  | 3        | 10      | All | All + custom |
```

**Timeline/Retention Diagrams:**

For anything involving time:

```
  TODAY                                              12 WEEKS AGO
    │                                                      │
    ▼                                                      ▼
   ┌─┬─┬─┬─┬─┬─┬─┐                                        ┌─┐
   │█│█│█│█│█│█│█│ ◀── Daily backups (7 days)             │░│
   └─┴─┴─┴─┴─┴─┴─┘                                        └─┘
   S M T W T F S
```

**Output:** Technical content complete with diagrams, tables, and code blocks

---

### Phase 4: POLISH

*The swan preens each feather until it gleams, perfect in the light...*

Apply Grove voice and validate formatting:

**Voice Checklist (no AI-coded words):**
- ❌ No "robust", "seamless", "leverage", "synergy"
- ❌ No em-dashes (use periods or commas)
- ❌ No "not X, but Y" patterns
- ✅ Short paragraphs (2-3 sentences max)
- ✅ Direct, clear statements
- ✅ Poetic closers that are earned, not forced

**Structure Validation:**
- [ ] Frontmatter with all required fields
- [ ] `aliases: []` included (even if empty)
- [ ] Date format: `Monday, January 6th 2026`
- [ ] `type: tech-spec` or appropriate type
- [ ] ASCII art header present
- [ ] Poetic tagline in italics after art
- [ ] Public/Internal names listed
- [ ] Domain specified (if applicable)

**Visual Content:**
- [ ] At least one ASCII flow diagram (if process-based)
- [ ] UI mockups included (if describing interface)
- [ ] Tables for comparisons where appropriate
- [ ] Code blocks for technical details
- [ ] No walls of text without visual breaks

**Validation Checklist:**

Before finalizing any spec, verify:

### Structure
- [ ] Frontmatter present with all required fields
- [ ] `aliases: []` included (even if empty)
- [ ] Date format correct (Day, Month Ordinal Year)
- [ ] `type: tech-spec` or appropriate type
- [ ] ASCII art header present after frontmatter
- [ ] Poetic tagline in italics
- [ ] Public/Internal names listed
- [ ] Domain specified (if applicable)

### Visual Content
- [ ] At least one ASCII flow diagram (if process-based)
- [ ] UI mockups included (if has UI)
- [ ] Tables for comparisons where appropriate
- [ ] Code blocks for technical details
- [ ] No walls of text without visual breaks

### Voice
- [ ] No em-dashes (use periods or commas)
- [ ] No "not X, but Y" patterns
- [ ] No AI-coded words (robust, seamless, leverage, etc.)
- [ ] Short paragraphs
- [ ] Poetic closers earned, not forced

### Completeness
- [ ] Overview/Goals section
- [ ] Architecture diagram
- [ ] Technical details (API, schema)
- [ ] Security considerations
- [ ] Implementation checklist

**Output:** Spec polished with proper Grove voice and validated structure

---

### Phase 5: LAUNCH

*The swan takes flight, the spec released into the world...*

**Final Review:**

Read the spec with fresh eyes:
1. Does it feel like a storybook entry?
2. Would you want to read this at 2 AM?
3. Is the nature metaphor clear and consistent?
4. Are the diagrams readable?

**Implementation Checklist:**

Every spec must end with actionable items:

```markdown
## Implementation Checklist

- [ ] Task 1
- [ ] Task 2
- [ ] Task 3
```

**Integration Check:**
- Does this skill need other animals?
- Does it reference related specs?
- Are dependencies documented?

**Output:** Spec complete, validated, and ready for implementation

---

## Swan Rules

### Elegance
Every element earns its place. The swan doesn't add decoration for decoration's sake. Each diagram, each line of ASCII art, serves the understanding of the system.

### Grace
Move deliberately through the phases. Don't rush to implementation details before the vision is clear. A spec written without understanding the metaphor will feel hollow.

### Beauty
Specs are storybook entries. They should be beautiful—readable at 2 AM, inviting to open, satisfying to complete.

### Communication
Use design metaphors:
- "Seeing the whole lake..." (understanding scope)
- "Tracing patterns..." (creating structure)
- "Adding detail to feathers..." (writing technical content)
- "Preening until it gleams..." (polishing voice)
- "Taking flight..." (releasing the spec)

---

## Creating ASCII Art

### The Process

1. **Identify the core metaphor** — What natural thing does this represent?
2. **Sketch the concept** — What visual would convey this at a glance?
3. **Choose your characters** — Box drawing, emoji, or creative ASCII
4. **Build in layers** — Start with outline, add detail, add flourishes
5. **Add the tagline** — Poetic one-liner that captures the essence

### Character Palette

**Box Drawing (safe, consistent):**
```
┌─────┬─────┐    ╭─────╮
│     │     │    │     │
├─────┼─────┤    ╰─────╯
│     │     │
└─────┴─────┘
```

**Lines and Arrows:**
```
→ ← ↑ ↓ ↔ ↕
▶ ◀ ▲ ▼
⟿ ⟸ ⟹
```

**Nature Emoji (use sparingly):**
```
🌲 🌳 🌿 🍂 🍃 🌸 🌺 🌻 🌷 🌱 🍄
☀️ 🌤️ ⭐ ✨ 💧 🔥
🦋 🐛 🐌
```

**Decorative:**
```
· ∙ • ° ˚ ∘
~ ≈ ∿
═ ║ ╔ ╗ ╚ ╝
░ ▒ ▓ █
```

### Tips

- Keep ASCII art under 20 lines tall
- Center the art within its code block
- Include breathing room (empty lines above/below)
- Test in a monospace font
- Consider mobile rendering (simpler is better)

---

## Anti-Patterns

**The swan does NOT:**
- Write specs without a nature metaphor
- Skip the ASCII art header
- Create walls of text without visual breaks
- Use AI-coded corporate language
- Rush through phases to get to implementation
- Forget the implementation checklist

---

## Example Design

**User:** "Write a spec for the new analytics system"

**Swan flow:**

1. 🦢 **VISION** — "Analytics tracks growth over time. Nature metaphor: Heartwood rings—each ring a story, each layer growth."

2. 🦢 **SKETCH** — "Create frontmatter, ASCII art of tree rings, introduction with tagline 'Every ring: a year, a story, a layer of growth'"

3. 🦢 **REFINE** — "Write architecture with flow diagram, API schema, comparison table of metrics"

4. 🦢 **POLISH** — "Apply Grove voice, validate no AI words, check all required elements"

5. 🦢 **LAUNCH** — "Final review, implementation checklist, release"

---

## Quick Decision Guide

| Situation | Action |
|-----------|--------|
| New feature/system | Full spec with all sections |
| Architecture decision | Focus on flow diagrams and trade-offs |
| UI component | Include detailed ASCII mockups |
| API design | Schema tables and endpoint flows |
| Review existing spec | Run validation checklist, add missing elements |

---

## Integration with Other Skills

**Before Writing:**
- `walking-through-the-grove` — If naming a new feature, complete naming first
- `chameleon-adapt` — If the spec involves UI patterns

**While Writing:**
- `owl-archive` — Apply Grove voice, avoid AI patterns

**After Writing:**
- This skill (`swan-design`) — Run validation checklist

**Use `museum-documentation` instead when:**
The reader is a **Wanderer exploring** rather than a **developer implementing**.

| Use swan-design | Use museum-documentation |
|----------------|-------------------------|
| Technical specifications | "How it works" for visitors |
| Architecture decisions | Codebase guided tours |
| Implementation plans | Knowledge base exhibits |
| Internal system docs | Narrative explanations |

---

*A good spec is one you'd want to read at 2 AM. Make it beautiful.* 🦢

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/neversight) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
