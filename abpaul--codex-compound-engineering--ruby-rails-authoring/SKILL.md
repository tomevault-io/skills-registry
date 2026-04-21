---
name: ruby-rails-authoring
description: Apply Rails-first server-rendered architecture conventions with Phlex UI, Stimulus behavior, and Turbo/Hotwire Native navigation. Use when this capability is needed.
metadata:
  author: abpaul
---

# Ruby Rails Authoring

## Coverage

- Rails application and engine code conventions
- Server-rendered UI architecture with Phlex components
- Stimulus behavior and Turbo/Hotwire Native navigation patterns
- Gem API and README authoring quality

## Architecture Defaults

- Build new product surfaces as server-rendered Rails pages first.
- Use Phlex for all reusable UI primitives, layouts, and composite components.
- Use Stimulus for client-side behavior; keep each controller focused on one responsibility.
- Use Turbo Drive/Frames/Streams for navigation and partial updates.
- Keep mobile shells aligned with Hotwire Native navigation patterns and route structure.
- Use Tailwind + daisyUI only through project-owned semantic tokens (color/type/spacing/radii/elevation/motion).
- Define project-specific palette, fonts, and illustration language as semantic token inputs.
- Override daisyUI themes as needed and never ship framework defaults unchanged.
- Use MD3 as structural guidance for anatomy/states/accessibility/interaction, not as visual identity.

## Workflow

1. Prefer clear domain-centric APIs and keep controllers thin.
2. Define or extend tokenized Phlex components before page-level markup.
3. Add Stimulus only where progressive enhancement or stateful behavior is needed.
4. Prefer Turbo flows over custom client-side SPA state.
5. Validate strong params, authorization, and N+1 prevention on hot paths.
6. Keep docs concise and example-driven using Rails/Phlex/Turbo conventions.

## UI Guardrails

- No ad-hoc styling in templates/components (`style=""`, raw hex values, arbitrary spacing literals).
- Map color/type/spacing/elevation/motion decisions to shared tokens.
- Component APIs expose semantic variants/sizes/states, not raw class override hooks.
- Every interactive surface defines focus, disabled, loading, and error behavior.
- Capture screenshot-polish evidence for changed UI surfaces across required web/native shells.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/abpaul) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
