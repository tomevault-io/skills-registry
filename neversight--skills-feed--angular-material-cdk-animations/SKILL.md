---
name: angular-material-cdk-animations
description: Angular Material + CDK + @angular/animations usage patterns aligned with zoneless + signals-first UI and M3 tokens. Use when this capability is needed.
metadata:
  author: neversight
---

# SKILL: Angular Material + CDK + Animations

## Use when
- Adding or refactoring UI that uses `@angular/material`, `@angular/cdk`, or `@angular/animations`.
- Building overlays, drag-drop, virtual scroll, focus management, or motion/transition behaviors.

## Workflow
1. Start from the simplest Material component that fits; only drop to CDK primitives when needed.
2. Keep inputs/outputs **signal-first**; avoid component-internal RxJS state.
3. If using CDK overlays/focus traps/observers, define ownership and **dispose on destroy**.
4. If adding motion:
   - CSS transitions for small state changes
   - `@angular/animations` only for coordinated/complex transitions
   - always respect `prefers-reduced-motion`
5. Validate a11y: labels, focus order, keyboard operation, and visible focus.

## Checklist (PR-ready)
- Uses M3 tokens (no raw hex/px).
- Overlays/listeners/observers cleaned up.
- Reduced-motion path exists.
- No “cute” drag/animation that adds complexity without user value.

## References
- `.github/instructions/62-angular-core-ui-copilot-instructions.md`
- `.github/instructions/71-angular-material-cdk-animations-copilot-instructions.md`
- `.github/skills/material-design-3/SKILL.md`
- `.github/skills/angular-ecosystem/SKILL.md`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/neversight) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
