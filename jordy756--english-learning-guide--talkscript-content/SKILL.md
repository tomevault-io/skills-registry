---
name: talkscript-content
description: Generate educational grammar content for TalkScript Use when this capability is needed.
metadata:
  author: jordy756
---

# TalkScript Content Generator

Generate Spanish-language educational content explaining English grammar in plain, accessible language. JavaScript code blocks (`javascript`) may illustrate structural patterns — but prose must never rely on programming metaphors.

> Global rules (language, tone, audience, MDX standards, prohibited elements) are defined in [`AGENTS.md`](../../../AGENTS.md). This skill covers only content-specific rules.

---

## Templates — Read Before Writing

Before generating any content, read and follow these template files exactly:

- [`templates/frontmatter.md`](templates/frontmatter.md) — Frontmatter structure
- [`templates/common-errors.md`](templates/common-errors.md) — "Errores comunes" section
- [`templates/practice.md`](templates/practice.md) — "Práctica" section
- [`templates/examples.md`](templates/examples.md) — Inline text examples

---

## Core Principles

1. **Clarity over cleverness** — Direct, accessible explanations
2. **Brevity** — 3 to 5 minute read maximum per page
3. **Visual patterns** — Code shows structure, never executable logic
4. **Consistency** — Uniform section order across all pages
5. **Practical examples** — Real-world usage with Spanish translations
6. **Strategic tabs** — Use `<Tabs>` to reduce scrolling, not to decorate

---

## Component Usage

### `<Aside>`

- Always include a descriptive `title` — never a generic label like "Nota" alone
- Allowed types: `note`, `tip`, `caution`, `danger`

### `<Card>` / `<CardGrid>`

- Only for grouping conceptual information
- Never place code blocks inside `<Card>`

### Code Blocks

- Language: always `javascript`
- Maximum 10 lines per block, ~50 characters per line
- No more than 3 code blocks per major section
- Identifiers must be in English
- Allowed directly in document flow or inside `<TabItem>` — never inside `<Card>`

**Choosing the right JS structure:**

Analyze what the grammar concept is showing and pick the JS construct that represents it most clearly. Do not default to objects for everything — use whatever makes the pattern most readable. Vary the structure across blocks within the same page.

| Grammar concept | Suggested JS structure |
|---|---|
| Conjugation table / forms | `object` with descriptive keys |
| Word order / sequences | `array` of strings |
| Transformation rules | `function` (input → output) |
| Conditional use / context | `if / else` or ternary |
| Sentence templates | template literal or string concat |
| Categories / classification | `object` with grouped arrays |
| Step-by-step formation | chained expressions or multi-line |

Never use the same structure in every block of a page. If a block would look identical to another, reconsider whether it adds value.

### `<Tabs>` / `<TabItem>`

- Use to group related variations: affirmative/negative/interrogative, tenses, formality levels
- Each `<TabItem>` must be self-contained — its own explanation and examples
- Include a code block per tab when applicable

---

## Document Structure

Every content page must follow this exact section order:

### 1. Frontmatter

Follow [`templates/frontmatter.md`](templates/frontmatter.md).

### 2. Import Statement

```javascript
import { ComponentA, ComponentB } from "@astrojs/starlight/components";
```

Import only components that appear in the file.

### 3. Introduction

One paragraph, minimum 3 lines and maximum 4 lines. Explain what the topic is, when it is used, and why it matters for the learner. Plain language only — no programming references.

### 4. Main Content

- **Simple topics** — 2 to 3 sections
- **Intermediate topics** — sections with strategic `<Tabs>`
- **Complex topics** — as many sections as needed, never pad

Separate major sections with `---`.

### 5. "Errores comunes"

Follow [`templates/common-errors.md`](templates/common-errors.md). Heading: `## Errores comunes`.

### 6. "Práctica"

Follow [`templates/practice.md`](templates/practice.md). Heading: `## Práctica`. Always last.

---

## Inline Text Examples

Follow [`templates/examples.md`](templates/examples.md). Place after explanations or code blocks.

- **Bold** only the word(s) illustrating the grammar point
- Spanish translation in parentheses at the end of each item
- 3 to 5 examples per block, varying subjects and contexts

---

## Text Formatting

- **Bold** for key grammar terms introduced for the first time
- `inline code` for short English examples embedded in prose
- Short paragraphs — 2 to 4 sentences maximum

---

## Validation Checklist

Run this before delivering any generated or modified file.

**Structure**

- [ ] Frontmatter matches [`templates/frontmatter.md`](templates/frontmatter.md)
- [ ] Only used components are imported
- [ ] Introduction is one paragraph between 3 and 4 lines
- [ ] Sections follow the required order
- [ ] "Errores comunes" matches [`templates/common-errors.md`](templates/common-errors.md)
- [ ] "Práctica" matches [`templates/practice.md`](templates/practice.md) and is last

**Content**

- [ ] Inline examples match [`templates/examples.md`](templates/examples.md)
- [ ] No code inside `<Card>` components
- [ ] No programming metaphors or developer jargon in prose
- [ ] All `<Aside>` components have descriptive titles
- [ ] Code blocks within line and character limits
- [ ] All code identifiers in English

**Global compliance** (see [`AGENTS.md`](../../../AGENTS.md))

- [ ] All prose in Spanish, all code identifiers in English
- [ ] No prohibited elements (emojis outside "Errores comunes", strikethrough, native tables, native alerts)
- [ ] Valid MDX — blank lines around components, self-closing tags, correct nesting

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jordy756) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
