---
name: style-extraction
description: Reverse-engineer a complete prose style guide from an existing book's text, identifying POV, tense, tone, vocabulary, dialogue conventions, and anti-patterns. Inverse of language-styling. Use when the user wants to extract style, analyze writing style, reverse-engineer voice, or mentions style extraction or style analysis. Use when this capability is needed.
metadata:
  author: ekrenzin
---

# Style Extraction

## Purpose

Analyze an entire book's prose to produce a style guide in the same format as the language-styling skill's output. The extracted guide captures what the author actually does, not what they claim to do -- derived entirely from textual evidence.

## Workflow

1. **Read chapter analyses** from `analysis/<book-title>/extracted/chapters/*/analysis.md`
2. **Sample raw chapters** -- Read at least 3 chapters spread across the book (early, middle, late) from `analysis/<book-title>/raw-chapters/`
3. **Synthesize patterns** across all chapters
4. **Generate the style guide** following the structure below
5. **Write to file** at `analysis/<book-title>/extracted/style-guide.md`
6. **Review with user** -- Walk through key findings with textual evidence

## Style Guide Structure

Produce the same format as the language-styling skill so extracted and authored guides are directly comparable:

```markdown
# Extracted Style Guide: [BOOK TITLE] by [AUTHOR]

## Point of View

- **POV type**: (first / third limited / third omniscient / second / mixed)
- **If multiple POVs**: Which characters? How are transitions handled?
- **Psychic distance**: How deep into the character's head? Does it vary?
- **Evidence**: [cite specific passages showing POV choices]

## Tense

- **Primary tense**: (past / present)
- **Exceptions**: (flashbacks, frame narratives, etc.)
- **Consistency**: Any tense slips or deliberate shifts?
- **Evidence**: [cite examples]

## Tone

- **Overall mood**: (specific descriptor, not just "dark" or "light")
- **Emotional range**: What is the ceiling and floor?
- **Humor**: Present? What kind? How frequent?
- **Tonal shifts**: Does tone change between chapters, scenes, or characters?
- **Evidence**: [contrasting passages showing range]

## Prose Density

- **Sentence length**: (measured -- short avg, long avg, overall avg from samples)
- **Paragraph length**: (typical range)
- **Description-to-dialogue ratio**: (approximate percentage)
- **White space usage**: How much breathing room does the prose give?
- **Evidence**: [representative paragraphs at different densities]

## Vocabulary Profile

- **Reading level**: (literary / commercial / YA / middle grade)
- **Vocabulary range**: (narrow/precise, broad/varied, specialized)
- **Domain-specific terms**: List recurring specialized vocabulary
- **Crutch words**: Words or phrases the author overuses (with frequency)
- **Avoided words**: Patterns of avoidance (e.g., never uses adverbs, avoids "very")
- **Evidence**: [specific word choices with page/chapter references]

## Dialogue Conventions

- **Tag style**: ("said" dominant / varied tags / action beats / tagless runs)
- **Tag frequency**: How often are lines tagged vs. untagged?
- **Internal monologue format**: (italics / integrated / stream-of-consciousness)
- **Dialogue punctuation style**: Any non-standard choices?
- **Character voice differentiation**: How distinct are individual voices?
- **Evidence**: [example dialogue passages showing conventions]

## Scene Structure Patterns

- **Scene openings**: How do scenes typically begin? (in media res / setting / thought)
- **Scene closings**: How do scenes typically end? (hook / resolution / beat)
- **Transition style**: How does the author move between scenes and chapters?
- **Chapter opening patterns**: First lines of several chapters for pattern analysis
- **Chapter closing patterns**: Last lines of several chapters
- **Evidence**: [cite opening and closing lines]

## Sensory Writing

- **Dominant senses**: Which senses appear most frequently?
- **Sensory frequency**: How often does sensory detail appear per page?
- **Metaphor/simile patterns**: Source domains the author draws from
- **Figurative language density**: (sparse / moderate / rich)
- **Evidence**: [strongest sensory passages]

## Pacing Markers

- **Action scene technique**: What changes in the prose during high-tension moments?
- **Emotional scene technique**: What changes during introspective moments?
- **Exposition method**: How is backstory and information delivered?
- **Chapter length variation**: Range and pattern
- **Evidence**: [contrasting passages showing pacing shifts]

## Anti-Patterns Detected

List specific recurring weaknesses or habits:

- (e.g., "Overuses 'suddenly' -- appears 47 times across 20 chapters")
- (e.g., "Characters sigh before speaking in 30% of dialogue beats")
- (e.g., "Adverbs on dialogue tags appear in chapters 1-5, then disappear")

## Style Evolution

Does the author's style change across the book?

- **Early chapters vs. late chapters**: Any measurable shifts?
- **Growing confidence or experimentation**: Where?
- **Consistency rating**: How uniform is the style?
```

## Guidelines

- **Quantify when possible**: "Short sentences" is weak. "Average sentence length of 11 words in action scenes, 24 in reflective scenes" is strong.
- **Sample broadly**: Never base a style conclusion on one chapter. Check at least 3 spread across the book.
- **Distinguish deliberate from accidental**: A repeated pattern might be a signature style or a crutch. Note which you think it is, and why.
- **Compare against norms**: When useful, note how the author's style compares to genre conventions.

## Validation Checklist

- [ ] Every section includes specific textual evidence
- [ ] POV and tense are confirmed across multiple chapters
- [ ] Vocabulary analysis includes crutch word counts
- [ ] Dialogue conventions are supported with example passages
- [ ] Anti-patterns are specific and quantified where possible
- [ ] Style evolution across the book is addressed
- [ ] Style guide is saved to `analysis/<book-title>/extracted/style-guide.md`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ekrenzin) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
