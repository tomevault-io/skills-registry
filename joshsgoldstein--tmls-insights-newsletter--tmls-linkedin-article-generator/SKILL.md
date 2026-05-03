---
name: tmls-linkedin-article-newsletter-version
description: Generate LinkedIn teaser articles for the AI in Production Field Notes newsletter. Use when asked to create a LinkedIn post/article from a Substack article, or when given a full newsletter article to turn into a LinkedIn teaser. Triggers on requests like "create LinkedIn article", "generate LinkedIn teaser", "write LinkedIn post for this Substack". Use when this capability is needed.
metadata:
  author: joshsgoldstein
---

# AI in Production Field Notes — LinkedIn Article Generator

Generate LinkedIn teaser articles from full Substack editions using a two-step extract → generate workflow.

## Workflow

**Step 1 — Extract:** Pull out the key elements:
- Opening hook scenario
- Core insight/framework
- Why this matters / the problem
- Key practices (3-5)
- What the full article covers (for CTA)

**Step 2 — Generate:** Using those extracted elements + the template structure, write the LinkedIn teaser.

The extraction step forces identification of what matters before writing — better quality output.

## Template Structure

Follow the 8-section structure in [references/template.md](references/template.md):

1. **Opening hook** — Vivid scenario (2-4 sentences)
2. **Author intro** — 👋 Hi everyone! We're Dave, Josh, and Anu...
3. **Teaser framing** — This week's edition is all about [TOPIC]...
4. **"Why this breaks" section** — Problem framing
5. **Core framework / key insight** — Numbered principles
6. **Practical takeaways** — Bullet points
7. **Closing CTA** — 👉 Read the full edition...
8. **Sign-off** — We hope you enjoy!

## Style Notes

- **Tone:** Conversational but expert. Like explaining to a smart colleague.
- **Length:** 400-600 words
- **Emojis:** 👋 for greeting, 👉 for CTAs. Sparingly elsewhere.
- **Formatting:** Short paragraphs, bullet points, bold key terms
- **Hook formula:** [Failure scenario] + [Why it's surprising] + [Stakes]

## References

- [references/template.md](references/template.md) — Full 8-section template with placeholders
- [references/prompt.md](references/prompt.md) — The two-step prompt workflow
- [references/examples.md](references/examples.md) — Example LinkedIn articles showing the style

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/joshsgoldstein) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
