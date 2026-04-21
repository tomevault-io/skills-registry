---
name: chapter-writing
description: Write full prose chapters from scene outlines, enforcing the style guide and maintaining character voice consistency. Use when the user wants to write a chapter, write a scene, produce prose, draft a chapter, or mentions chapter writing or scene writing. Use when this capability is needed.
metadata:
  author: ekrenzin
---

# Chapter Writing

## Purpose

Transform a scene outline into polished prose that obeys the style guide, maintains character voice, and delivers the emotional experience planned in the outline.

## Workflow

1. **Read all reference materials** (mandatory, every time):
   - `books/<book-title>/style/style-guide.md`
   - `books/<book-title>/chapters/chNN/outline.md`
   - All character sheets for characters appearing in this chapter
   - The previous chapter's prose (for continuity and flow)
   - `books/<book-title>/world/world-bible.md` (for setting details)
2. **Write the chapter** scene by scene, following the outline
3. **Self-audit** against the style guide and character voices
4. **Write to file** at `books/<book-title>/chapters/chNN/chapter.md`
5. **Trigger character-sheet-update** -- This is mandatory after every chapter

## Writing Process Per Scene

For each scene in the outline:

### Before Writing

- Confirm the POV character's voice by re-reading their speech pattern notes
- Note the entering emotional state
- Identify the sensory focus for this scene

### During Writing

- Open the scene with grounding: where, when, what the POV character notices first
- Write dialogue in the character's established voice -- refer to their speech patterns
- Maintain the planned emotional trajectory from the outline
- Deliver the goal/obstacle/outcome structure
- Weave in sensory details from the world bible
- End the scene with the planned transition

### After Writing Each Scene

Quick check:

- Does the dialogue sound like THIS character, not a generic character?
- Is the prose consistent with the style guide?
- Does the scene accomplish what the outline specified?

## Character Voice Enforcement

This is the most critical element. Before writing any dialogue or internal monologue:

1. Open the character's sheet
2. Read their speech pattern section
3. Write a test line of dialogue (do not include in final prose)
4. Verify it matches their vocabulary, rhythm, and emotional register
5. Only then write the actual dialogue

**Red flags that a character's voice has slipped:**

- All characters use the same sentence length
- Dialogue could be swapped between characters without anyone noticing
- A character uses vocabulary outside their established range
- A character reacts emotionally in a way that contradicts their personality profile

## Style Guide Compliance

After completing the chapter draft, audit for:

- [ ] Correct POV and tense throughout
- [ ] No banned words or phrases from the Anti-Patterns list
- [ ] Dialogue tags match style guide rules
- [ ] Sensory writing meets minimum requirements
- [ ] Pacing matches the scene type (action = short sentences, emotion = long)
- [ ] Scene openings and closings follow the style conventions

## Continuity Audit

After writing, verify:

- [ ] No character knows something they should not know yet
- [ ] Physical details match (injuries, clothing, time of day, weather)
- [ ] Objects and MacGuffins are accounted for
- [ ] Emotional states flow naturally from the previous chapter
- [ ] Any promises planted are noted for future payoff

## Output Format

The chapter file should contain:

```markdown
# Chapter [N]: [TITLE]

[prose content, with scene breaks indicated by --- between scenes]
```

## Post-Writing (Mandatory)

After the chapter is written, immediately invoke the **character-sheet-update** skill to update all character sheets for characters who appeared in or were affected by this chapter. This step is not optional.

## Validation Checklist

- [ ] All scenes from the outline are written
- [ ] Style guide compliance audit passes
- [ ] Every character's dialogue matches their voice profile
- [ ] Continuity audit passes
- [ ] Chapter is saved to `books/<book-title>/chapters/chNN/chapter.md`
- [ ] Character-sheet-update skill is triggered

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ekrenzin) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
