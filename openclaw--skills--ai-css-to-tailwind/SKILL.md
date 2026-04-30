---
name: css-to-tailwind
description: Convert CSS files to Tailwind utility classes Use when this capability is needed.
metadata:
  author: openclaw
---

# CSS to Tailwind

Got legacy CSS? Convert it to Tailwind classes. Handles complex selectors and media queries.

## Quick Start

```bash
npx ai-css-to-tailwind ./src/styles/button.css
```

## What It Does

- Converts CSS rules to Tailwind utilities
- Handles responsive breakpoints
- Converts custom properties to theme values
- Preserves complex hover/focus states

## Usage Examples

```bash
# Convert a CSS file
npx ai-css-to-tailwind ./styles/header.css

# Convert and update component
npx ai-css-to-tailwind ./styles/card.css --update ./components/Card.tsx

# Batch convert
npx ai-css-to-tailwind ./styles/*.css
```

## What It Handles

- Media queries → responsive prefixes
- Pseudo-classes → state variants
- CSS variables → theme tokens
- Complex selectors → component patterns

## Requirements

Node.js 18+. OPENAI_API_KEY required.

## License

MIT. Free forever.

---

**Built by LXGIC Studios**

- GitHub: [github.com/lxgicstudios/ai-css-to-tailwind](https://github.com/lxgicstudios/ai-css-to-tailwind)
- Twitter: [@lxgicstudios](https://x.com/lxgicstudios)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/openclaw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
