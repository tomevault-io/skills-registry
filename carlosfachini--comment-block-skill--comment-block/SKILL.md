---
name: comment-block
description: Insert structured START/END code comment blocks around features, sections, logical blocks, TODOs, bugfixes, refactors, or notes. Use when a user asks for Comment Block, says "use comment-block", "comment-block:", "comment-block manual", "comment-block config", or wants an AI agent to add standardized code comments directly into source files across HTML, Vue, React JSX/TSX, JavaScript, TypeScript, CSS, PHP, Python, Shell, Ruby, JSONC, and similar codebases. Use when this capability is needed.
metadata:
  author: carlosfachini
---

# Comment Block

Insert structured, visually framed comments directly into user code around a complete feature, section, or logical block.

## Origin

- Author: Carlos Fachini
- Source: https://github.com/carlosfachini/comment-block-skill
- License: MIT

## Operating Modes

- **Assisted mode, default:** analyze the active file, diff, selection, or surrounding code. Propose type, title, body, date, optional author, and insertion location. Ask for confirmation before editing.
- **Manual mode:** when the user says `comment-block manual` or `use comment-block manual`, ask only for missing title and body. Use `FEATURE` unless the user provides or clearly implies another type.
- **Quick mode:** when the user writes `comment-block: Title | Body`, parse the left side as title and the right side as body. Confirm only when the insertion location is ambiguous or risky.
- **Config mode:** when the user says `comment-block config`, explain optional settings: default author, default type, confirmation behavior, and language detection preferences. Do not edit code unless asked.

## Comment Data

- `type`: one of `FEATURE`, `SECTION`, `LOGIC`, `TODO`, `BUGFIX`, `REFACTOR`, `NOTE`.
- Default `type`: `FEATURE`.
- Infer type from wording when clear. Examples: "fix" -> `BUGFIX`, "refactor" -> `REFACTOR`, "todo" -> `TODO`, "note" -> `NOTE`, "logic" -> `LOGIC`, "section" -> `SECTION`.
- `title`: concise, uppercased in the inserted block.
- `date`: current local date in `YYYY-MM-DD`.
- `author`: optional. Include only when configured or explicitly requested, such as "with author Carlos".
- `body`: one or more clear sentences describing why the block exists.

## Assisted Confirmation

Before editing in assisted mode, show:

```text
Suggestion:
Type: FEATURE
Title: Final Price Calculation
Body: Centralizes subtotal, discount, and total rules displayed in the summary.
Date: 2026-04-24
Location: before the PriceSummary component.

Apply?
```

Proceed only after confirmation. If the user already supplied exact content and an unambiguous location, a brief confirmation is enough.

## Placement Rules

- Insert before the complete feature, section, declaration, selector, component, route, function, class, block, or template node being documented.
- If the user asks for wrapping a region and the syntax supports comments after the region, place a matching end comment after the complete region. Otherwise use the preferred single block format that contains both `[START: TYPE]` and `[END: TYPE]` before the region.
- Never insert inside string literals, template literal content, JSX prop lists, tag attributes, object keys, import/export clauses, decorators, or partially selected syntax.
- Do not comment strict `.json`. Only comment JSON-like files when they are JSONC, JSON5, VS Code settings, or another comments-enabled format.
- In mixed PHP files, choose syntax from the current region: PHP code uses `/* */`; HTML/template regions use `<!-- -->`.
- Ask for clarification when multiple plausible blocks match the request or when the only possible insertion point may break syntax.

## Syntax Selection

Use the smallest valid comment wrapper for the current language context. Inside that wrapper, always use the Comment Block visual frame:

- A `[START: TYPE]` line.
- A full-width `#` border line.
- Metadata and body lines wrapped with `||` side rails.
- A full-width `#` border line.
- An `[END: TYPE]` line.

Prefer a readable fixed frame width around 72 characters when practical. Pad `||` lines with spaces so the closing `||` aligns. If content is longer than the frame width, wrap it across multiple `||` lines instead of widening the frame excessively.

### HTML And Vue Templates

```html
<!-- [START: FEATURE]
########################################################################
|| Title: HERO SECTION                                                ||
|| Date: 2026-04-24                                                   ||
||                                                                    ||
|| Defines the main visible landing section.                          ||
########################################################################
[END: FEATURE] -->
<section>...</section>
```

### React JSX And TSX

Use JSX block comments outside prop lists and inside JSX expression braces:

```tsx
{/* [START: FEATURE]
########################################################################
|| Title: FINAL PRICE CALCULATION                                     ||
|| Date: 2026-04-24                                                   ||
||                                                                    ||
|| Centralizes subtotal, discount, and total rules.                   ||
########################################################################
[END: FEATURE] */}
<PriceSummary />
```

In normal JavaScript or TypeScript regions of a JSX/TSX file, use `/* */`.

### JavaScript, TypeScript, CSS, PHP Code, JSONC

```js
/* [START: FEATURE]
########################################################################
|| Title: FINAL PRICE CALCULATION                                     ||
|| Date: 2026-04-24                                                   ||
||                                                                    ||
|| Centralizes subtotal, discount, and total rules.                   ||
########################################################################
[END: FEATURE] */
```

### Python, Shell, Ruby

```python
# [START: FEATURE]
# ######################################################################
# || Title: FINAL PRICE CALCULATION                                   ||
# || Date: 2026-04-24                                                 ||
# ||                                                                  ||
# || Centralizes subtotal, discount, and total rules.                 ||
# ######################################################################
# [END: FEATURE]
```

## Formatting Rules

- Preserve surrounding indentation.
- Add exactly one empty `||                                                                    ||` rail line between metadata and body.
- Add `Author: ...` immediately after `Date: ...` when author is included.
- Keep title uppercase in the inserted block.
- Always include the visual `#` border and `||` side rails.
- Prefer ASCII punctuation unless the surrounding file already uses a different convention.

---
> Source: [carlosfachini/comment-block-skill](https://github.com/carlosfachini/comment-block-skill) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-22 -->
