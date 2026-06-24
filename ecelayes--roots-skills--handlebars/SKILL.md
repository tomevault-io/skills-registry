---
name: handlebars-pure-patterns
description: Best practices for writing clean, logic-less Handlebars (hbs) templates. Use when this capability is needed.
metadata:
  author: ecelayes
---

# Handlebars Templating Standards

## The "Logic-less" Philosophy
1.  **Data Preparation:** Templates should strictly DISPLAY data. All filtering, sorting, or complex math MUST happen in the application code (Controller/Service) before the data reaches the template.
2.  **Avoid Spaghettis:** If you need more than two nested `{{#if}}` or `{{#each}}` blocks, consider refactoring the data structure or creating a custom helper.

## Syntax & Helpers
1.  **Escaping:**
    - Default `{{ value }}` is safe and escapes HTML characters.
    - Use `{{{ value }}}` (triple-stash) ONLY for trusted HTML strings (e.g., pre-sanitized content).
2.  **Custom Helpers:**
    - Do not write complex inline logic. Instead of `{{#if (eq (mod index 2) 0)}}`, create a helper `{{#if (isEven index)}}` or, better yet, pre-calculate `isEven` in the data model.
    - Register helpers for formatting dates, currencies, and translation.

## Component Architecture (Partials)
1.  **Atomic Design:** Break complex UIs into small files in the `partials/` directory.
    - Naming convention: `_card.hbs`, `_navbar.hbs`.
2.  **Context Passing:**
    - Explicit Context: `{{> myPartial specificData }}` is preferred over implicit context inheritance.
    - Block Partials: Use `{{#> layout}} ... {{/layout}}` for wrapping content (like slots).

## Troubleshooting
- **Missing Properties:** Handlebars fails silently on undefined properties. Ensure your data object structure strictly matches the template expectations.
- **Prototypes:** If using Mongoose/ORMs, convert documents to Plain Old JavaScript Objects (POJOs) (e.g., `.lean()` or `.toJSON()`) before passing to Handlebars to avoid prototype access issues.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ecelayes) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
