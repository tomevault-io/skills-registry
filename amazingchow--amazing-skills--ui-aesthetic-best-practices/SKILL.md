---
name: ui-aesthetic-best-practices
description: Turn 3-5 reference websites or a Figma reference board into a reusable design system, AI guardrails, and more original high-aesthetic UI directions. Use when Codex needs to extract visual DNA, scaffold a design-system workspace, write CLAUDE/Cursor rules, generate tasteful pages without copying a source site, or review an existing UI that feels template-like, over-decorated, or too close to a reference. Also use for requests like "做更有审美的 UI", "先沉淀设计系统再出页面", "把参考站整理成 design system", or "用 Figma MCP 提炼风格". Use when this capability is needed.
metadata:
  author: amazingchow
---

# Build Tasteful UI Systems

Use this skill to convert inspiration into a system first, and pages second. Favor reusable foundations, semantic tokens, components, and patterns over one-off page styling.

## Workflow

1. Pick the right entry mode.
   - If no design-system workspace exists yet, scaffold one with `scripts/scaffold_design_system.py`.
   - If the user only has live websites, first build a reference board from 3-5 sources.
   - If the user already has a Figma reference board, use the bundled Figma MCP prompt to extract system layers instead of drawing pages directly.
   - If the user already has a weak or derivative UI, audit it against the originality and quality bar, then repair foundations, tokens, and patterns before polishing screens.
2. Scaffold the working directory before authoring.
   - Copy `assets/design-system-template/` into the target project with the script below.
   - Keep the folder structure intact so future agents can discover the system quickly.
3. Build or clean the reference board.
   - Use 3-5 sources and assign each one a primary responsibility such as typography rhythm, color mood, hierarchy, whitespace density, or illustration direction.
   - De-brand the board aggressively: remove logos, slogans, distinct iconography, branded illustrations, and proprietary copy.
   - Use `assets/design-system-template/utils/REFERENCE_BOARD_CHECKLIST.md` to verify the board is abstractable instead of copy-prone.
4. Extract the design system in this order.
   - `foundations/`: color, typography, spacing, radius, shadow, motion.
   - `tokens/semantic/`: stable usage names such as `text/primary` or `surface/raised`.
   - `components/`: reusable families with variants, states, and prohibitions.
   - `patterns/`: section-level composition rules that stop the UI from becoming a card pile.
   - Read the specific template README inside `assets/design-system-template/` only for the layer you are filling.
5. Write AI guardrails after the system exists.
   - Customize `assets/design-system-template/prompts-rules/CLAUDE.template.md` and `assets/design-system-template/prompts-rules/cursor-ui-design.mdc`.
   - Encode mood keywords, reuse rules, token priority, forbidden copying behaviors, and originality requirements.
6. Generate demo directions before business pages.
   - Produce 2-3 distinct directions first.
   - For each direction, name the reused tokens, components, and patterns, and state at least 3 deliberate differences from the references.
   - Use `assets/design-system-template/utils/UI_PROMPT_TEMPLATE.md` for page generation and `assets/design-system-template/utils/DEMO_BOARD_CHECKLIST.md` to judge readiness.
7. Implement the production UI from the system.
   - Reuse existing semantic tokens and components before inventing new ones.
   - Prefer hierarchy, whitespace, composition, and typography over decorative effects.
   - If the result still looks like a reskinned reference, go back to `tokens/semantic` and `patterns/` instead of tweaking surface styling forever.

## Review Mode

When reviewing an existing UI, prioritize these findings:

- Missing system evidence: hard-coded values, inconsistent component families, or layout decisions that do not map back to tokens and patterns.
- Reference leakage: copied section order, copied illustration logic, copied hero structure, or recognizably borrowed brand voice.
- Template collapse: centered hero plus two buttons by default, every section turning into a card grid, or decoration replacing information hierarchy.
- Over-styling: too many accents, heavy gradients, glassmorphism, shadows, or motion used as a substitute for strong structure.

Use [references/originality-quality-bar.md](references/originality-quality-bar.md) when you need a fuller audit checklist or pass/fail criteria.

## Commands

Scaffold the bundled design-system template into a project:

```bash
python3 scripts/scaffold_design_system.py \
  --output /absolute/path/to/project/design-system-template
```

Overwrite an existing scaffold intentionally:

```bash
python3 scripts/scaffold_design_system.py \
  --output /absolute/path/to/project/design-system-template \
  --force
```

## Heuristics

- Use at least 3 reference sources. Fewer than 3 sharply increases imitation risk.
- Let each reference contribute one main dimension. Do not blend five full-page layouts together.
- Keep one dominant accent. Let typography, spacing, and composition do most of the aesthetic work.
- Change at least 3 of these when drafting new pages: grid, proportions, whitespace rhythm, corner treatment, border strategy, shadow strategy.
- If a component looks attractive only because of gradients, images, or visual effects, strengthen its structure first.
- Prefer system edits over page-local fixes. Change foundations or token mappings before patching individual sections.

## Resources

- Use `scripts/scaffold_design_system.py` to copy the bundled design-system template deterministically into a project workspace.
- Read [references/workflow.md](references/workflow.md) for the full end-to-end process, including repair workflows for an already-existing UI.
- Read [references/resource-map.md](references/resource-map.md) to jump directly to the right bundled template or checklist for the current task.
- Read [references/originality-quality-bar.md](references/originality-quality-bar.md) when reviewing originality risk, system completeness, or aesthetic quality.

The bundled `assets/design-system-template/` contains the reusable folder structure, prompt templates, rule templates, and checklists that came from the original best-practices package.

---
> Source: [amazingchow/amazing-skills](https://github.com/amazingchow/amazing-skills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
