---
name: ui-search-filtering
description: Implementing fast, client-side search for large documentation sets. Use when this capability is needed.
metadata:
  author: jcorpac
---

# UI Search & Filtering

With 80+ skills, users need to find exactly what they need in milliseconds.

## Implementation Patterns
- **Index Generation**: Create a JSON index containing titles, tags, and small snippets of every skill.
- **Fuzzy Search**: Use a library like `Fuse.js` or `FlexSearch` for tolerant, high-performance querying.

## Interaction Design
- **Real-time Results**: Trigger search on every keystroke (`input` event).
- **Result Highlight**: Visually highlight the matching terms in the result cards.

## Best Practices
- **Keyboard Navigation**: Allow users to focus and navigate search results using arrow keys and Enter.
- **Zero Results State**: Provide a clear "No skills found" message with suggestions.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jcorpac) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
