---
name: kate-top-edit
description: Top-editing skill for Every drafts. Use when the user says "run Kate's top edit skill" or asks for a top edit on a piece. Screens for common issues Kate catches in final review before publication. Use when this capability is needed.
metadata:
  author: everyinc
---

# Kate's Top Edit

Scan the draft for the issues below. Output a sequential checklist the editor can work through while reviewing the piece.

## Output Format

Return findings in document order as a simple checklist:
```
- [ ] "[quoted text from draft]" → [suggested fix]
```

No category labels. No explanations of why something is an issue. Just the problem text and the fix.

## Checklist

1. **Vague "This/That" openers** — Sentences starting with "This" or "That" without a specific noun. Replace with what "this" refers to.

2. **Unsourced quotes and data** — Quoted material or statistics without hyperlinks. Flag for sourcing.

3. **AI tells** — Flag any of the following patterns:
   - Correlative constructions: "not only...but also," "both...and," "either...or," "whether...or"
   - Clichés and filler: "at the end of the day," "it's worth noting," "the reality is," "in today's world," "when it comes to"
   - Overuse of "real," "really," "actually," "truly," "essentially," "fundamentally"
   - Clipped three-part lists: "X, Y, and Z" appearing repeatedly, especially with abstract nouns
   - Excessive em dashes — especially multiple per paragraph
   - Stacked adverbs or intensifiers
   - "Straightforward" or "simple" to describe complex things
   - Formulaic transitions: "That said," "With that in mind," "Here's the thing"
   - Sycophantic openers: "Great question," "Excellent point"

4. **Floating quotes** — Quotes introduced only with "As [person] says" without context. Add setup in the author's own words.

5. **Jargon** — Technical terms a smart lay reader wouldn't know. Suggest plain-language alternatives.

6. **Missing Every links** — Search the Every archive for relevant pieces that could be linked. Suggest specific articles with URLs.

7. **Written-out numbers above 10** — Should be numerals.

8. **Unidentified people** — Names without context on who they are or what they're known for.

9. **Hedging phrases** — "I've found that," "We've discovered that," "I think," "I believe," "It seems like." Rewrite to state the point directly.

10. **Marketing speak** — Buzzwords, hype language, grandiose claims. Examples: "game-changer," "revolutionary," "unlock," "supercharge," "next-level," "cutting-edge."

11. **Sentence fragments** — Phrases that should be complete sentences.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/everyinc) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-12 -->
