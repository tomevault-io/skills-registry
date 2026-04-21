---
name: character-extraction
description: Extract character sheets from an existing book's text, building personality profiles, speech patterns, relationships, and arcs from textual evidence. Inverse of character-design. Use when the user wants to extract characters, analyze characters, build character profiles from a book, or mentions character extraction or character analysis. Use when this capability is needed.
metadata:
  author: ekrenzin
---

# Character Extraction

## Purpose

Build complete character sheets from an existing book's text, using the same template as the character-design skill. Every field must be supported by textual evidence. The result is a set of character sheets that could be used to write the same characters in new material.

## Workflow

1. **Read chapter analyses and scene maps** to identify all named characters and their appearances
2. **Read raw chapters** to gather direct evidence for each character
3. **Classify characters** -- Determine who gets a full sheet vs. a minimal sheet
4. **Generate character sheets** using the template at `templates/character-sheet.md`
5. **Run the differentiation check** -- Compare all extracted characters against each other
6. **Write to files** at `analysis/<book-title>/extracted/characters/<character-name>.md`
7. **Review with user**

## Character Census

Before building sheets, create a character census:

```markdown
# Character Census: [BOOK TITLE]

## Full Sheet Characters (appear in 2+ scenes or are plot-critical)

| Character | First Appearance | Chapters Active | Role |
| --------- | ---------------- | --------------- | ---- |
|           |                  |                 |      |

## Minimal Sheet Characters (walk-ons, single scene)

| Character | Appearance | Function |
| --------- | ---------- | -------- |
|           |            |          |
```

Write the census to `analysis/<book-title>/extracted/characters/census.md`.

## Extraction Method Per Character

### Identity

- Name, aliases, age, gender, occupation -- extracted from narration, dialogue, and context clues
- Flag anything that is implied rather than stated

### Physical Appearance

- Only include what the text actually describes
- Note the method: Does the author describe appearance directly, through other characters' observations, through action, or not at all?

### Personality Profile (The Inverse Five Layers)

Reverse-engineer the character-design skill's five personality layers:

1. **Core Drive**: What does this character pursue across the entire book? Not a plot goal -- the psychological need underneath.
   - **Evidence**: [scenes that reveal the drive]

2. **Wound**: What past event made them this way? Look for backstory reveals, flashbacks, avoidance behaviors.
   - **Evidence**: [passages that hint at or reveal the wound]

3. **Mask**: How do they present themselves to others? The gap between public behavior and private truth.
   - **Evidence**: [contrast between how they act publicly vs. privately]

4. **Contradiction**: Where does their behavior contradict their stated values?
   - **Evidence**: [specific moments of contradiction]

5. **Breaking Point**: When does the mask crack? The moment the wound is exposed.
   - **Evidence**: [the scene(s) where this happens]

### Speech Pattern Extraction

This is critical. For each character, extract:

- **Vocabulary level**: Formal? Slang? Technical? Regional?
- **Sentence structure**: Short and punchy? Long and winding?
- **Default emotional register**: Sarcastic? Earnest? Guarded?
- **Verbal tics/catchphrases**: Any repeated phrases or patterns?
- **What they avoid saying**: Topics or words they dance around
- **Voice under stress**: How does their speech change when angry, afraid, lying?
- **Sample lines**: Collect 5-8 representative dialogue lines that capture the voice

### Relationships

Build the relationship table by tracking every interaction between characters:

| Character | Relationship | Dynamic | Tension |
| --------- | ------------ | ------- | ------- |
|           |              |         |         |

Note: Build relationships from BOTH sides. If A is extracted, and A interacts with B, note A's view of B. When B is extracted, note B's view of A.

### Arc Tracking

Map the character's arc across the book:

| Chapter | Key Event | Emotional Shift | Relationship Change | New Knowledge |
| ------- | --------- | --------------- | ------------------- | ------------- |
|         |           |                 |                     |               |

Fill in starting state, current state (end of book), and the full chapter log.

## The Uniqueness Audit

After extracting all characters, apply the character-design skill's Uniqueness Mandate:

1. **Voice test**: If all names and descriptions were removed, could you tell the characters apart by dialogue alone?
2. **Conflict test**: Does each character want something that puts them in conflict with at least one other character?
3. **Flaw test**: Does each character have a flaw that is specific and non-generic?
4. **Decision test**: Would each character make a different decision than the protagonist in the same situation?

Report results. These are not criticisms of the author -- they are analytical observations about the cast's construction.

## Guidelines

- **Text is truth**: Only attribute traits that the text supports. Speculation must be clearly labeled as interpretation.
- **Show your work**: Every personality claim needs a scene reference or quoted passage.
- **No projection**: Do not assign motivations the text does not support just because they would make a good character sheet.
- **Track uncertainty**: Use confidence levels (confirmed / strongly implied / speculative) for traits without direct evidence.

## Validation Checklist

- [ ] Character census is complete
- [ ] Every full-sheet character has all five personality layers addressed (even if some are "not evident in text")
- [ ] Speech patterns include representative sample lines
- [ ] Relationships are built from both sides
- [ ] Arc tracking covers the full book
- [ ] Uniqueness audit results are documented
- [ ] Character sheets are saved to `analysis/<book-title>/extracted/characters/`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ekrenzin) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
