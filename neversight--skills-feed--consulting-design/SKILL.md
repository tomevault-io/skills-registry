---
name: consulting-design
description: Consult Gemini AI for architecture alternatives, design trade-offs, and brainstorming. Use when seeking different perspectives on design, evaluating architectural approaches, comparing solutions, or generating creative ideas. Use when this capability is needed.
metadata:
  author: neversight
---

# Design Consultation with Gemini

## Tools

| Tool                      | Use For                                     |
| ------------------------- | ------------------------------------------- |
| `mcp__gemini__ask-gemini` | Architecture, design review, trade-offs     |
| `mcp__gemini__brainstorm` | Generating alternatives, creative solutions |

## ask-gemini

```json
{
  "prompt": "I'm designing [system]. Current approach: [desc]. Trade-offs and alternatives?",
  "model": "gemini-2.5-pro"
}
```

Options: `changeMode: true` for structured edits, `sandbox: true` for code execution

## brainstorm

```json
{
  "prompt": "How might we [challenge]?",
  "domain": "software",
  "methodology": "auto",
  "ideaCount": 10
}
```

Methodologies: `divergent`, `convergent`, `scamper`, `design-thinking`, `lateral`, `auto`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/neversight) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
