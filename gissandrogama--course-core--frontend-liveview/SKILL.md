---
name: frontend-liveview
description: Use when building or editing Phoenix LiveView frontend: LiveViews, HEEx templates, components, layouts, forms, Tailwind CSS, or LiveView tests in CourseCore. Covers HEEx conventions, core_components, streams, routing, and project-specific frontend rules.
metadata:
  author: gissandrogama
---

# Frontend LiveView (CourseCore)

**Resumo (pt-BR):** Convenções de frontend do projeto: LiveView em `live/`, HEEx com Layouts.app e current_scope, formulários com to_form e <.input>, navegação com <.link navigate/patch>, ícones <.icon>, listas grandes com streams, Tailwind sem @apply e sem daisyUI.

Apply this skill when working on the **frontend** of CourseCore: LiveViews, components, templates, styles, or LiveView tests.

## Scope

- **LiveView modules**: `lib/course_core_web/live/` (suffix `Live`, e.g. `CourseCoreWeb.CursoLive.Index`)
- **Components**: `lib/course_core_web/components/` (core_components, layouts, page components)
- **Router**: live routes in `lib/course_core_web/router.ex` (scope and `live_session` when applicable)
- **Templates**: HEEx (`~H` or `.html.heex`)
- **Styles**: Tailwind CSS and CSS in `assets/css/`; **no** `@apply`; **no** daisyUI
- **JS/Hooks**: `assets/js/`; **no** inline `<script>` in templates
- **Tests**: `test/course_core_web/live/`

## Layout and root

- **Always** start LiveView templates with `<Layouts.app flash={@flash} ...>` wrapping all inner content
- Pass `current_scope` to `<Layouts.app>` when using authenticated routes; fix missing `current_scope` by moving routes to the proper `live_session`
- `<.flash_group>` is only in the Layouts module—do not call it from elsewhere

## Forms

- In the LiveView: always use `to_form/2` (from a changeset or params); assign as `@form`
- In the template: **always** use `<.form for={@form}>` and `<.input field={@form[:field]}>` from core_components
- **Never** expose the changeset directly in the template; **never** use `<.form let={f} ...>`
- Give the form a unique DOM id (e.g. `id="curso-form"`) for tests

## Navigation

- Use `<.link navigate={href}>` and `<.link patch={href}>` in templates
- Use `push_navigate` and `push_patch` in LiveView callbacks
- **Do not** use deprecated `live_redirect` or `live_patch`

## Icons and inputs

- **Always** use the `<.icon name="hero-..." class="..." />` component from core_components for icons
- **Never** use Heroicons modules directly
- **Always** use the imported `<.input>` component for form inputs; if you override its `class`, your custom classes must fully style the input (defaults are not inherited)

## Dynamic lists and streams

- For **large or dynamic lists**, use **streams**: `stream(socket, :items, list)`, `stream_delete(socket, :items, item)`, `stream(..., reset: true)` for filtering
- In the template: set `phx-update="stream"` on the parent and a stable DOM `id` (e.g. `id="items"`); use the stream assign and the same id for each child
- **Do not** use `phx-update="append"` or `phx-update="prepend"` (deprecated)
- Streams are not enumerable; to filter/refresh, refetch data and re-stream with `reset: true`; use a separate assign for counts or empty state

## Tailwind and CSS

- Use Tailwind v4 import syntax in `app.css` as in the project (e.g. `@import "tailwindcss" source(none);` and `@source` for project paths)
- **Never** use `@apply` in raw CSS
- Build custom Tailwind-based components manually; do not use daisyUI
- Only `app.js` and `app.css` are supported; vendor scripts must be imported into these bundles, not linked externally
- **Never** write inline `<script>` in HEEx templates; put JS in `assets/js/` and hooks in app.js

## UI/UX

- Produce polished, responsive layouts with Tailwind
- Use subtle micro-interactions (hover, transitions, loading states)
- Clean typography, spacing, and visual hierarchy
- For a distinctive aesthetic, use the **frontend-design** skill together with this one

## Testing

- Add unique DOM ids to forms and key elements so tests can use `element/2`, `has_element?/2`, etc.
- Prefer testing outcomes and presence of key elements over raw HTML or implementation details
- LiveView tests: use `Phoenix.LiveViewTest` and `render_submit/2`, `render_change/2` for forms

## After changes

- Run `mix precommit` when done
- Ensure LiveView and UI tests pass

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/gissandrogama) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
