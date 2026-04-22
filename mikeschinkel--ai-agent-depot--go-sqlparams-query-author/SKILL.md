---
name: go-sqlparams-query-author
description: Write and refactor SQL query code using Mike's go-sqlparams package. Use when building parameterized SQL, constructing WHERE clauses, pagination/sorting, or binding named params. Use when this capability is needed.
metadata:
  author: mikeschinkel
---

# SQL Params Query Author

Use this skill when the user asks to:
- create SQL queries with parameters
- refactor raw SQL + manual args into go-sqlparams
- build dynamic filters (optional clauses) safely
- implement pagination, sorting, and query composition

## Mandatory references (read in order)
1) `references/nonnegotiables.md`
2) `references/doterr.md`
3) `references/clearpath.md` (production code only)
4) `references/sqlparams-package-notes.md` (go-sqlparams catalog notes)

## Hard constraints
- Never build SQL by concatenating untrusted values.
- Use parameter binding patterns from go-sqlparams; do not invent your own.
- Preserve behavior when refactoring unless the user explicitly asks to change semantics.
- Return errors via doterr.

## Composition approach (default)
- Keep SQL text readable (multi-line raw string literals).
- Use go-sqlparams to manage:
  - named parameters / argument lists
  - optional predicates
  - IN lists / repeated parameters (as supported by the package)
- Build query pieces (SELECT, FROM, WHERE, ORDER, LIMIT/OFFSET) in a small number of clear steps.

## Deliverables
When asked to implement a query:
- Provide the query builder function(s)
- Show the execution call site (db.Query / db.QueryContext etc. as appropriate)
- Include row scanning code (or mapping helpers) if relevant
- Include a short note on indexes/constraints if the query implies them (optional)

## Self-check
- No compound init-if.
- No ignored errors.
- No string concatenation for untrusted SQL values.
- Uses go-sqlparams for parameter management and doterr for errors.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mikeschinkel) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
