---
name: refactor
description: Step-by-step procedure for refactoring backend models or frontend components in this repo. Use when asked to "refactor X", clean up a method/component, or reduce duplication — anything that changes structure without changing behavior. Enforces the behavior-/query-preserving rules from CLAUDE.md. Use when this capability is needed.
metadata:
  author: cobel1024
---

# Refactor

A refactor in this repo changes **structure, not behavior**. The CLAUDE.md `## Refactoring`
section has the always-on rules; this skill is the procedure to follow them.

## Procedure

1. **Confirm scope.** A bare "refactor X" = behavior- and query-shape-preserving readability
   work. NOT a redesign, NOT a new abstraction, NOT a strategy pattern. If the request seems
   to want more than that, ask before proceeding.

2. **Read the whole file.** Read the target model/component file end to end and map every
   relationship it touches (root CLAUDE.md "Before Writing Any Code"). Never refactor from a
   partial view.

3. **Inventory the invariants — these must survive untouched:**
   - `# [query-reviewed]` / `# [N+1-reviewed]` comments → the code below them is intentional.
   - `@track_fields` hooks (`on_*_changed`), custom signals, `@pghistory.track()` → side effects.
   - Raw SQL (`connection.cursor()`), `apps.get_model()` dynamic dispatch → kept on purpose.
   - Backend: the queryset shape (`select_related` / `prefetch_related` / `annotate` / `gather`).
   - Frontend: SolidJS reactivity (`<For>`/`<Show>`, store access patterns), generated API client.

4. **Check test coverage.** `grep -rln "<target>" core/apps/{app}/tests/` (backend) or find the
   relevant `*.test.tsx` (frontend). If the behavior isn't covered, write a characterization
   test **first**, confirm it passes against the current code, then refactor.

5. **Make the minimal change.** Improve readability with named locals, guard clauses, and
   comments before considering extraction. Do not split a long method into single-use private
   helpers just because it is long (see CLAUDE.md "Never"). Reuse existing shared abstractions
   instead of inventing new ones.

6. **Verify.**
   - Backend: `docker compose exec minima pytest core/apps/{app}/tests/ -v` and `uv run dev.py lint`.
   - Frontend: `cd web && npm run lint` and the relevant `npm run build` / vitest.
   - Confirm query count did not regress for backend changes.

7. **Report.** State what changed, what you deliberately left alone (especially `[query-reviewed]`
   spots and any code you judged was intentional duplication, not accidental), and the test/lint result.

## When NOT to refactor

- The four assessment apps (`exam` / `assignment` / `discussion` / `quiz`) look parallel
  (`SessionDict`, `QuestionPool`, `Attempt.start/submit`, `Grade.grade`) but their bodies are
  domain-specific. The shared layer already lives in the mixins (`LearningObjectMixin`,
  `GradeFieldMixin`, etc.). Do **not** collapse them into a shared base class.
- `studio` and `desk` field/admin layers look similar but bind to different stores
  (studio: Paper/initEditing; desk: staging context + `DeskModelUnionSpec`). Verify the binding
  divergence before attempting any cross-realm extraction.
- Intentional, domain-meaningful repetition is not duplication. When in doubt, leave it and say so.

---
> Source: [cobel1024/minima](https://github.com/cobel1024/minima) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-26 -->
