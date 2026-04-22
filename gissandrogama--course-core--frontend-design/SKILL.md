---
name: frontend-design
description: Create distinctive, production-grade frontend interfaces with high design quality. Use when the user asks to build web components, pages, or applications (LiveView/HEEx, React, Vue, or HTML/CSS/JS). Generates creative, polished code that avoids generic AI aesthetics. Use when this capability is needed.
metadata:
  author: gissandrogama
---

# Frontend Design

**Resumo (pt-BR):** Guia para interfaces de frontend com direção estética clara e produção de alto nível. Evitar estética genérica (tipografias e gradientes batidos); priorizar tipografia, cor, movimento e composição com intenção.

This skill guides creation of distinctive, production-grade frontend interfaces that avoid generic "AI slop" aesthetics. Implement real working code with exceptional attention to aesthetic details and creative choices.

## Design Thinking

Before coding, understand the context and commit to a **BOLD aesthetic direction**:

- **Purpose**: What problem does this interface solve? Who uses it?
- **Tone**: Pick a clear direction: brutally minimal, maximalist chaos, retro-futuristic, organic/natural, luxury/refined, playful/toy-like, editorial/magazine, brutalist/raw, art deco/geometric, soft/pastel, industrial/utilitarian, etc. Use for inspiration but design something true to the chosen direction.
- **Constraints**: Technical requirements (framework, performance, accessibility).
- **Differentiation**: What makes this UNFORGETTABLE? What's the one thing someone will remember?

**CRITICAL**: Choose a clear conceptual direction and execute it with precision. Bold maximalism and refined minimalism both work—the key is **intentionality**, not intensity.

Then implement working code that is:
- Production-grade and functional
- Visually striking and memorable
- Cohesive with a clear aesthetic point-of-view
- Meticulously refined in every detail

## Frontend Aesthetics Guidelines

- **Typography**: Choose fonts that are distinctive and interesting. Avoid generic fonts (Arial, Inter); use characterful choices. Pair a distinctive display font with a refined body font.
- **Color & Theme**: Commit to a cohesive aesthetic. Use CSS variables for consistency. Dominant colors with sharp accents outperform timid, evenly-distributed palettes.
- **Motion**: Use animations for effects and micro-interactions. Prefer CSS-only when possible. Focus on high-impact moments: one well-orchestrated page load with staggered reveals creates more delight than scattered micro-interactions. Use scroll-triggering and hover states that add surprise.
- **Spatial Composition**: Consider unexpected layouts—asymmetry, overlap, diagonal flow, grid-breaking elements. Generous negative space OR controlled density.
- **Backgrounds & Visual Details**: Create atmosphere and depth. Add contextual effects and textures: gradient meshes, noise textures, geometric patterns, layered transparencies, dramatic shadows, decorative borders, grain overlays.

**NEVER** use generic AI aesthetics: overused fonts (Inter, Roboto, Arial, system fonts), clichéd color schemes (e.g. purple gradients on white), predictable layouts and cookie-cutter patterns. Interpret creatively and make unexpected choices. No design should look the same; vary themes, fonts, and aesthetics.

**IMPORTANT**: Match implementation complexity to the vision. Maximalist designs need elaborate code and effects; minimalist designs need restraint, precision, and subtle details. Elegance comes from executing the vision well.

## In Phoenix LiveView / HEEx projects

Apply the same principles: Tailwind classes and custom CSS in `assets/css/`, no `@apply` in raw CSS, no daisyUI. Use the project's `<.input>`, `<.icon>`, and layout components; style with intentional typography, color, spacing, and motion within those constraints.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/gissandrogama) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
