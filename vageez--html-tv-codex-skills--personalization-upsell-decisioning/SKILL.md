---
name: personalization-upsell-decisioning
description: Build remote-navigable personalization and upsell experiences for TV apps. Use when designing interruption policy, CTA prominence, dismiss/revisit behavior, and funnel instrumentation across browse, details, and playback surfaces. Use when this capability is needed.
metadata:
  author: vageez
---

# Personalization Upsell Decisioning

## Overview
Deliver monetization and personalization prompts without breaking leanback flow.
Gate interruption intensity by context, entitlement, and user momentum.

## Decisioning Rules
- Prefer non-blocking surfaces during exploration.
- Escalate to interruptive prompts only at high-intent moments.
- Always provide dismiss path and predictable revisit entry point.

## Workflow
1. Define surfaces (home hero, rail card, details CTA, playback pause layer).
2. Assign prompt type per surface (inline, modal, banner).
3. Define eligibility rules using entitlement, history, and cooldown windows.
4. Ensure every prompt is fully directional-key navigable.
5. Track impression, focus, click, dismiss, and conversion events.

## UX Constraints
- No modal upsell during fragile transitions (resume, auth recovery).
- Back closes upsell layer first, then returns to prior context.
- Re-entry must restore focus to stable prior target.

## Validation Checklist
- Interruptive prompt frequency stays within configured caps.
- Dismissed prompts are suppressible but discoverable later.
- Funnel events are complete and attributable by surface.

## References
- Use `references/funnel-events.md` for event taxonomy and KPI mapping.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/vageez) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
