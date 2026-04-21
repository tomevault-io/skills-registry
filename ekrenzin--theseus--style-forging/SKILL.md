---
name: style-forging
description: Fork an extracted style guide into a distinct new prose voice, preserving structural decisions that work while developing original vocabulary, rhythm, and conventions. Use when the user wants to forge a new style, fork a voice, create a derivative style guide, or mentions style forging, voice divergence, or prose transmutation. Use when this capability is needed.
metadata:
  author: ekrenzin
---

# Style Forging

## Purpose

Take an extracted style guide and the derivative blueprint, then produce an original style guide that learns from the source's craft decisions without imitating its voice. The result is a style guide in the same format as the language-styling skill's output, ready to govern chapter writing.

## Workflow

1. **Read the derivative blueprint** from `books/<book-title>/derivative-blueprint.md`
2. **Read the diverged world bible** from `books/<book-title>/world/world-bible.md`
3. **Read source style guide(s)** from `analysis/<source>/extracted/style-guide.md`
4. **Classify each style element** using the forge/keep/invent framework
5. **Generate the new style guide** following the method below
6. **Run the voice distinction test**
7. **Write to file** at `books/<book-title>/style/style-guide.md`
8. **Review with user**

## The Forge/Keep/Invent Framework

Classify every element of the source style guide into one of three categories:

### Keep (Structural Decisions)

Technical choices that are not voice -- they are architecture. These can be kept directly:
- POV type (first, third-limited, third-omniscient)
- Tense (past, present)
- Scene break conventions
- Chapter length targets
- Prose-to-dialogue ratio range

### Forge (Transform the Approach)

Craft decisions where the source's approach works but the specific execution must change:
- Tone (same family, different register)
- Prose density (same general level, different texture)
- Pacing strategy (same principle, different execution)
- Sensory writing approach (same depth, different dominant senses)
- Dialogue conventions (same realism level, different speech textures)

### Invent (Create from Scratch)

Voice elements that must be wholly original:
- Vocabulary signature (word choices that define the voice)
- Sentence rhythm (the music of the prose)
- Metaphor families (what the prose compares things to)
- Narrator personality (the invisible character telling the story)
- Anti-patterns (what THIS voice never does)

## Section-by-Section Forge Guide

### Point of View
- **Keepable**: POV type, psychic distance range
- **Forge**: How POV shifts within scenes, what triggers closer/further distance
- **Ask**: Does the diverged world demand a different POV relationship? (e.g., a world with telepathy might need closer psychic distance)

### Tense
- **Keepable**: Tense choice
- **Forge**: Tense-shifting patterns (flashbacks, prophecy, etc.)
- **Ask**: Does the story's relationship to time differ from the source?

### Tone
- **Forge entirely**: Tone is voice. Even if the blueprint says "Low divergence" on tone, the specific execution must be original.
- **Method**: Name the source tone precisely (e.g., "dry academic wit with undercurrent of dread"). Then shift at least one axis:
  - Same wit, different undercurrent (dry wit with melancholy instead of dread)
  - Same undercurrent, different surface (warm conversational with undercurrent of dread)
  - Same family, different temperature (dry wit becomes sharp wit)

### Prose Density
- **Keep**: General density level (sparse, moderate, dense)
- **Forge**: The texture of density. Dense prose can be lush and sensory OR dense and idea-heavy. Sparse prose can be clipped and hard OR spare and lyrical.
- **Method**: If source is "moderate, favoring action clarity," forge something like "moderate, favoring emotional undercurrent" -- same weight class, different fighting style.

### Vocabulary Profile
- **Invent entirely**: Vocabulary is the most recognizable element of voice.
- **Method**:
  1. Note the source's vocabulary level (commercial, literary, genre-specific)
  2. Choose the same general level for accessibility
  3. Build a completely different word palette:
     - Different domain metaphors (source uses military metaphors? Use natural/organic ones)
     - Different filler/connector patterns
     - Different technical terminology (rooted in your world, not theirs)
  4. Define 5-10 "signature words" -- words your narrator reaches for that the source narrator would not
  5. Define a "never use" list -- words the source overuses that your voice avoids

### Dialogue Conventions
- **Keep**: Realism level (stylized vs naturalistic)
- **Forge**: Tag conventions, dialect approach, subtext strategy
- **Invent**: If the source has a special convention (e.g., telepathy in brackets), create your own for your world's equivalent
- **Root in world**: Dialogue must sound like people from YOUR world talking

### Scene Structure
- **Keep**: General approach (in medias res, establishing shot, etc.)
- **Forge**: Specific patterns for openings, transitions, closings
- **Ask**: Does your world's rhythm of life suggest different scene rhythms?

### Sensory Writing
- **Keep**: Sensory density level
- **Forge**: Dominant senses (if source leads with visual, try leading with auditory or tactile)
- **Invent**: Sensory signatures unique to your world (what does magic smell like in THIS world?)

### Pacing Markers
- **Keep**: The principle (e.g., "shorter sentences in action")
- **Forge**: The specific techniques and thresholds
- **Invent**: Pacing techniques the source doesn't use

### Anti-Patterns
- **Include source's anti-patterns**: If the source overuses "sighed" and "suddenly," those go on your never-do list too -- learn from their mistakes
- **Add your own**: Based on the voice you're building, what would break it?
- **Add source-avoidance patterns**: Specific phrases, rhythms, or habits that would make your prose sound like the source

## The Voice Distinction Test

After generating the new style guide, test it:

1. **Paragraph Test**: Write a sample paragraph following the new style guide and another following the source style guide, describing the same scene. Are they clearly different voices? If not, increase divergence on vocabulary and rhythm.
2. **Dialogue Test**: Write the same exchange in both voices. Would a reader notice they're different? If not, redesign dialogue conventions.
3. **Crutch Test**: Does the new style guide have its own unique crutch-avoidance list that includes source-specific patterns?
4. **World Integration Test**: Does the vocabulary, metaphor family, and sensory palette connect to the diverged world? Could these descriptions only exist in THIS world?
5. **Blindfold Test**: If someone read a chapter written in each style, back to back, with no context, would they assume different authors? If not, diverge further.

## Guidelines

- **Voice is the hardest thing to diverge**: Readers recognize voice more than they recognize plot or world. Invest the most effort here.
- **Vocabulary is the fingerprint**: Change words first and most aggressively.
- **Rhythm is the accent**: Sentence length patterns, paragraph shapes, and punctuation habits are what make prose "sound" like someone.
- **Metaphor families are worldview**: What your narrator compares things to reveals how they see the world. This must be rooted in YOUR world.
- **Anti-patterns are boundaries**: A clear list of what the voice never does is as important as what it does.
- **Test with actual prose**: A style guide is theory. Write sample paragraphs to test that the theory produces a distinct voice in practice.

## Validation Checklist

- [ ] Every source style element is classified as Keep/Forge/Invent
- [ ] Tone is forged with at least one axis shifted
- [ ] Vocabulary profile has 5-10 signature words and a "never use" list
- [ ] Metaphor families are original and world-rooted
- [ ] Anti-patterns include source-specific avoidance patterns
- [ ] Voice Distinction Test passes (all 5 checks)
- [ ] Style guide follows the language-styling skill's format
- [ ] Style guide is written to `books/<book-title>/style/style-guide.md`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ekrenzin) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
