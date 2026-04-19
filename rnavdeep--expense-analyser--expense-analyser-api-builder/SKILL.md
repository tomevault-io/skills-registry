---
name: expense-analyser-api-builder
description: Build or extend API integrations in the expense-analyser Vue frontend. Use when adding a new backend endpoint or API-driven feature and you need to update this project's models, axios service methods, Pinia store actions/state, and UI wiring consistently across src/models, src/services, src/stores, and src/components or src/views. Use when this capability is needed.
metadata:
  author: rnavdeep
---

# Expense Analyser API Builder

Add API-backed capabilities by following the existing frontend architecture:
- DTOs in `src/models`
- axios wrappers in `src/services`
- business flow and UI state in `src/stores`
- component/view integration in `src/components` or `src/views`

Read `references/project-api-conventions.md` before implementing changes.

## Workflow

1. Identify where the new API belongs.
- Extend an existing service/store pair when the feature matches a current domain (`Expense`, `Auth`, `Friends`, `Notification`, `Document`, `Extract`).
- Create a new `src/services/<Domain>Service.ts` and `src/stores/<Domain>/index.ts` when introducing a new domain.

2. Define request/response models first.
- Add or update DTO/type files in `src/models`.
- Use explicit TypeScript types for service signatures and store state/action return values.

3. Implement service method(s) in `src/services`.
- Reuse `const BASE_URL = import.meta.env.VITE_APP_API_URL`.
- Keep domain-scoped `API_URL = BASE_URL + '/<ControllerName>'`.
- Call axios with `withCredentials: true` to match current auth/cookie flow.
- Set headers only when needed (for example multipart uploads).
- Throw meaningful errors via `axios.isAxiosError`.

4. Expose feature in Pinia store.
- Add loading/success/error flags to state when UI feedback is needed.
- Add an action that validates required inputs before calling the service.
- Normalize response handling for component use.

5. Wire to UI.
- Call the store action from target component/view.
- Bind loading and error state to existing Vuetify UX patterns.
- Update routing only if this feature introduces a new page.

6. Validate.
- Run `npm run type-check`.
- Run `npm run lint` for changed files.
- Confirm no broken imports and that new actions are reachable from UI flow.

## Guardrails

- Preserve existing path aliases (`@/models`, `@/services`).
- Match existing naming in touched files. Do not mass-rename legacy PascalCase methods unless requested.
- Keep API URL construction centralized inside service files.
- Do not call axios directly in components when a store already owns that domain.
- Keep `SKILL.md` concise; put project details in `references/project-api-conventions.md`.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/rnavdeep) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
