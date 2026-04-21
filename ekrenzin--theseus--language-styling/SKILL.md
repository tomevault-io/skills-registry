---
name: language-styling
description: Define and enforce prose style, voice, tense, POV, vocabulary level, and literary conventions for a book. Use when the user wants to set writing style, define voice, choose POV or tense, establish tone, or mentions style guide or language rules. Use when this capability is needed.
metadata:
  author: ekrenzin
---

# Language Styling

## Purpose

Establish a binding style guide for a book so that every chapter reads like it was written by one author with one vision.

## Workflow

1. **Read existing materials** -- Read the world bible and character sheets to understand the world's tone
2. **Gather preferences** -- Ask the user about their style goals or provide options
3. **Generate the style guide** following the structure below
4. **Write to file** at `books/<book-title>/style/style-guide.md`
5. **Review with user** -- Walk through key decisions

## Style Guide Structure

```markdown
# Style Guide: [BOOK TITLE]

## Point of View

- POV type: (first person / third limited / third omniscient / second person)
- If multiple POVs: which characters, and how are transitions handled?
- Psychic distance: how deep into the character's head do we go?

## Tense

- Primary tense: (past / present)
- Exceptions: (e.g., flashbacks in a different tense)

## Tone

- Overall mood: (e.g., grim, hopeful, sardonic, dreamlike, clinical)
- Emotional range: what is the ceiling and floor?
- Humor: is it present? What kind? How often?

## Prose Density

- Sentence length preference: (short and direct / varied / long and flowing)
- Paragraph length: (tight and punchy / medium / expansive)
- Description-to-dialogue ratio guidance

## Vocabulary Constraints

- Reading level target: (e.g., literary, commercial, YA, middle grade)
- Words and phrases to AVOID: (list specific banned words or crutch phrases)
- Domain-specific vocabulary to USE: (drawn from the world bible)

## Dialogue Rules

- Dialogue tag style: ("said" only / varied tags / action beats preferred)
- Internal monologue format: (italics / integrated / marked)
- How character voice differs from narrative voice

## Scene Structure Conventions

- How scenes open: (in media res / setting first / character thought)
- How scenes close: (hook / resolution / emotional beat)
- Transition style between scenes

## Sensory Writing Rules

- Minimum senses per scene: (e.g., at least 3 of 5 senses)
- Which senses to favor for this world
- Metaphor/simile frequency and source domain

## Pacing Markers

- Action scenes: short sentences, minimal interiority
- Emotional scenes: longer sentences, deep POV
- Exposition: woven into action, never standalone paragraphs

## Anti-Patterns (Never Do This)

- List specific prose sins to avoid for this book
- e.g., "No adverbs on dialogue tags"
- e.g., "Never open a chapter with weather"
- e.g., "No dream sequences"
```

## Style Decision Prompts

If the user is unsure, offer these choices:

**POV**: "Do you want the reader inside one head at a time (third limited), knowing everything (omniscient), or experiencing it as 'I' (first person)?"

**Tone**: "Pick the closest vibe: Cormac McCarthy (sparse, brutal), Neil Gaiman (whimsical, dark), Ursula Le Guin (precise, philosophical), Brandon Sanderson (clear, propulsive), Donna Tartt (lush, literary)."

**Pacing**: "Fast and commercial (short chapters, cliffhangers) or literary and immersive (long chapters, deep interiority)?"

## Enforcement

When writing chapters, the style guide is law. Before writing any chapter:

1. Re-read the style guide
2. After writing, audit the chapter against the Anti-Patterns list
3. Flag any deviations and correct them

## Validation Checklist

- [ ] POV and tense are explicitly defined
- [ ] Tone description is specific enough to distinguish from other books
- [ ] At least 5 Anti-Patterns are listed
- [ ] Vocabulary constraints reference the world bible
- [ ] Dialogue rules are concrete
- [ ] Style guide is saved to `books/<book-title>/style/style-guide.md`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ekrenzin) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
