---
name: angular-forms
description: Angular 20+ forms specialist for reactive forms, validation, submission flows, and form-state boundaries. Use after the `angular` router when a task centers on complex forms, validation UX, or form-driven workflows. Use when this capability is needed.
metadata:
  author: kizzz
---

# Angular Forms

## When to use

- Building or refactoring create/edit/filter forms
- Defining validation, submission, reset, dirty tracking, or wizard flows
- Mapping DTOs into form models and back
- Fixing form UX issues such as disabled states, validation timing, or duplicate submissions

## Do not use

- State architecture beyond the form boundary; use [`angular-state-management`](../angular-state-management/SKILL.md)
- Routing as the main concern; use [`angular-routing`](../angular-routing/SKILL.md)
- PrimeNG component choice as the main concern; use [`angular-primeng-enterprise`](../angular-primeng-enterprise/SKILL.md)
- Tests only; use [`angular-testing`](../angular-testing/SKILL.md)

## Instructions

1. Use stable Angular 20+ form patterns by default:
   - reactive forms for production workflows
   - typed controls where practical
   - explicit submit handlers
2. Keep one source of truth for editable state. Do not fight between form state and store state.
3. Separate concerns:
   - DTO mapping at the edge
   - validation rules near form construction
   - submit side effects in a service or orchestrator
4. Treat validation as UX:
   - show errors when fields are touched/submitted
   - disable duplicate submits
   - surface server validation cleanly
5. Use experimental form APIs only when the repo already adopted them explicitly.
6. Verify happy path, invalid path, loading state, and retry path.

## Default rules

- Prefer clear form models over ad hoc object mutation
- Keep templates declarative, not validation-heavy
- Normalize server errors into field or form messages
- Preserve dirty tracking for unsaved-change guards when relevant

## Related skills

- [`angular`](../angular/SKILL.md)
- [`angular-ui-patterns`](../angular-ui-patterns/SKILL.md)
- [`angular-state-management`](../angular-state-management/SKILL.md)
- [`angular-routing`](../angular-routing/SKILL.md)
- [`angular-testing`](../angular-testing/SKILL.md)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/kizzz) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
