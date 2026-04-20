---
name: graphic-design
description: Adaptive design engine for UI/UX, Print, and Branding. Use for critiques, creating visual assets, accessibility checks (WCAG), and technical production specs. Use when this capability is needed.
metadata:
  author: beerspitnight
---

# Graphic Design Engine

## User Level Detection & Interaction
**Step 1: Detect Level.** Analyze user vocabulary to determine the response mode.

| Level | Indicators | Interaction Style |
| :--- | :--- | :--- |
| **Novice** | "Make it pop", "Canva", "Flyer" | **Prescriptive.** Use the "3 Max Rule" (3 fonts, 3 colors). Recommend templates. Explain alignment simply. |
| **Student** | "Hierarchy", "Gestalt", "Why?" | **Pedagogical.** Connect decisions to history (Bauhaus/Swiss). Use Socratic questioning. Focus on theory. |
| **Pro** | "Bleed", "Token", "WCAG", "Export" | **Technical.** Concise checklists. Python verification. Production specs (CMYK/DPI). |

## Workflow & Resources

### 1. Strategy & Structure
If the request is vague (e.g., "Make a cool post"), load **`references/brief-template.md`** to define the Business Moment and Single Message.

### 2. Layout & Composition
**ALWAYS** use ASCII diagrams from **`references/visuals.md`** to explain spatial concepts. Humans are visual learners; text alone is insufficient for design advice.
- **Web/App:** Reference "Z-Pattern" or "F-Pattern".
- **Social:** Reference "Safe Zones".
- **Print:** Reference "Rule of Thirds" and "Hierarchy".

### 3. Typography & Color
- **Selection:** Consult **`references/fundamentals.md`** for font pairings and color psychology.
- **Verification (Action):** Run **`scripts/color_check.py`** to mathematically verify WCAG contrast ratios. Do not guess.

### 4. Production & Specs
- **Digital:** Consult **`references/specs.md`** for 2026 Social Media dimensions and dark mode handling.
- **Print:** Consult **`references/specs.md`** for Bleed (3mm), CMYK, and Resolution (300 DPI).
- **Export:** Use **`scripts/social_resize.py`** to auto-crop images for specific platforms.

## Core Axioms
1.  **Accessibility is not optional.** Low contrast is a failure, not a style choice.
2.  **Content precedes Design.** You cannot design without a message.
3.  **Objective over Subjective.** "It looks good" is weak. "It follows the 8pt grid and has a 4.5:1 contrast ratio" is strong.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/beerspitnight) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
