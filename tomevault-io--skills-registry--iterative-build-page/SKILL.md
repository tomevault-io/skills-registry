---
name: iterative-build-page
description: Builds a Nuxt 3 landing page section-by-section from natural language descriptions using RDS Vue UI components. Use when user describes page sections incrementally (e.g., "Add a hero with video background", "Now add a 3-column card grid").
metadata:
  author: chandima
---

# Iterative Build Page

## 1. Overview

Builds a landing page incrementally — the user describes sections one at a time and the agent
adds each section using RDS Vue UI design system components. The page grows interactively:
sections can be added, modified, reordered, or removed at any point. When the user is satisfied,
the agent finalizes the page into a production-ready Nuxt 3 `.vue` file with an accompanying
content JSON file.

This skill relies on the **rds-component-mapper** skill to translate natural language section
descriptions into the correct `@rds-vue-ui/*` component.

## 2. Workflow

1. **Scaffold** — Start with an empty page scaffold containing the layout wrapper and `<script setup>`.
2. **Describe** — The user describes a section in natural language.
3. **Map** — Use the rds-component-mapper decision tree to select the best-fit component.
4. **Generate** — Produce a Vue template snippet with the component and its props.
5. **Append** — Add the section to the page and display the current page state.
6. **Iterate** — The user can add more sections, modify existing ones, reorder, or remove.
7. **Summarize** — After every change, present the current page structure as a numbered list.
8. **Finalize** — When the user says "done" or "finalize", generate the final page with content JSON.

## 3. Supported Operations

| Command | Syntax | Description |
|---------|--------|-------------|
| Add | `add <description>` | Append a new section at the end of the page |
| Insert | `insert before/after <section>` | Insert a section at a specific position |
| Modify | `modify <section> <changes>` | Change props, content, or component of an existing section |
| Remove | `remove <section>` | Delete a section from the page |
| Reorder | `reorder <section> to position N` | Move a section to a different position |
| Preview | `preview` | Show the current page structure summary |
| Finalize | `finalize` | Generate final `.vue` page and content JSON |

Sections are referenced by their 1-based position number or by the section name shown in the
summary list (e.g., `modify 3 change title to "Our Programs"` or `remove hero`).

## 4. Section Description → Component Mapping

The following table shows common natural language descriptions and their corresponding RDS
component mappings. When the user describes a section, match against these patterns first.
For descriptions that do not match, fall back to the rds-component-mapper decision tree.

| Natural Language Description | RDS Component | Notes |
|------------------------------|---------------|-------|
| "Hero with dark gradient and centered title" | `HeroStandardApollo` | Use `theme` prop for gradient variant |
| "Hero with video background" | `HeroStandardApollo` | Set `videoSrc` prop |
| "Hero with image slider" | `HeroCarouselApollo` | Provide `slides` array |
| "3 cards showing programs" | `CardImageArticle` | Wrap in Bootstrap `row`/`col-md-4` grid |
| "Card grid with icons" | `CardIconArticle` | Wrap in Bootstrap grid |
| "FAQ accordion" | `OverlapAccordionAtlas` | Provide `items` array with question/answer pairs |
| "Student testimonial with photo" | `SectionTestimonialFalcon` | Set `quote`, `author`, `image` props |
| "Multiple testimonials carousel" | `CarouselTestimonialApollo` | Provide `testimonials` array |
| "Standard footer" | `FooterStandard` | Site-wide footer component |
| "Stats showing 50,000 graduates" | `SectionStatApollo` | Provide `stats` array with label/value pairs |
| "Image carousel" | `CarouselImageApollo` | Provide `images` array |
| "Full-width parallax image section" | `SectionParallaxApollo` | Set `backgroundImage` prop |
| "Content section with text and image" | `SectionApollo` | Standard content block |
| "Video embed" | `VideoApollo` | Set `videoId` or `src` prop |
| "Call-to-action banner" | `SectionApollo` | Use CTA variant with button props |
| "Navigation bar" | `NavbarStandard` | Top navigation with logo and links |

## 5. Page Scaffold

Every new page starts from this scaffold:

```vue
<template>
  <div>
    <!-- Sections will be added here -->
  </div>
</template>

<script setup>
// RDS components are auto-imported — no explicit imports needed.
// Content JSON is loaded from assets/content/<page-name>.json

const { data: content } = await useAsyncData('content', () =>
  import(`~/assets/content/<page-name>.json`).then(m => m.default)
)
</script>
```

The `<div>` wrapper serves as the root element. Each section is appended as a direct child.

## 6. Section Template Patterns

### Single Component Section

```vue
<!-- Section: Hero -->
<HeroStandardApollo
  :title="content.hero.title"
  :subtitle="content.hero.subtitle"
  :backgroundImage="content.hero.backgroundImage"
  theme="dark"
/>
```

### Grid of Repeated Components

```vue
<!-- Section: Programs -->
<div class="container py-5">
  <div class="row">
    <div
      v-for="(card, index) in content.programs.items"
      :key="index"
      class="col-md-4 mb-4"
    >
      <CardImageArticle
        :title="card.title"
        :image="card.image"
        :link="card.link"
        :body="card.body"
      />
    </div>
  </div>
</div>
```

### Section with Accordion Items

```vue
<!-- Section: FAQ -->
<div class="container py-5">
  <h2 class="text-center mb-4">{{ content.faq.title }}</h2>
  <OverlapAccordionAtlas :items="content.faq.items" />
</div>
```

## 7. Summary Display Format

After each operation, present the current page structure as a numbered summary:

```
Current Page Structure:
──────────────────────
1. Hero            → HeroStandardApollo        (dark gradient, centered title)
2. About           → SectionApollo             (text + image)
3. Programs        → CardImageArticle × 3      (3-column grid)
4. Testimonials    → SectionTestimonialFalcon   (single quote with photo)
5. FAQ             → OverlapAccordionAtlas      (5 items)
6. Footer          → FooterStandard
──────────────────────
Total: 6 sections | Ready to finalize
```

This summary keeps the user oriented and allows them to reference sections by number or name.

## 8. Output Format

When the user finalizes, generate two files:

### Page File: `pages/<page-name>.vue`

- Uses `<script setup>` with Composition API
- RDS components are auto-imported (no explicit import statements)
- Bootstrap 5 grid classes for layout (`container`, `row`, `col-*`)
- Content bound from the JSON file via `useAsyncData`
- Each section wrapped in semantic comments (`<!-- Section: Name -->`)

### Content File: `assets/content/<page-name>.json`

- Top-level keys correspond to section names (kebab-case)
- Each key contains the props/data for that section
- Arrays for repeated items (cards, accordion entries, testimonials)
- All text content is stored here — the Vue template contains no hardcoded strings

Example structure:

```json
{
  "hero": {
    "title": "Welcome to Our Program",
    "subtitle": "Discover opportunities",
    "backgroundImage": "/images/hero.jpg"
  },
  "programs": {
    "sectionTitle": "Our Programs",
    "items": [
      { "title": "Program A", "image": "/images/prog-a.jpg", "link": "/programs/a" },
      { "title": "Program B", "image": "/images/prog-b.jpg", "link": "/programs/b" },
      { "title": "Program C", "image": "/images/prog-c.jpg", "link": "/programs/c" }
    ]
  },
  "faq": {
    "title": "Frequently Asked Questions",
    "items": [
      { "question": "How do I apply?", "answer": "Visit our admissions page..." }
    ]
  }
}
```

## 9. Modification Rules

When modifying an existing section:

- **Change component** — Replace the template snippet entirely; preserve content keys in JSON.
- **Change props** — Update the relevant key-value pairs in the content JSON.
- **Change layout** — Adjust Bootstrap grid classes (e.g., `col-md-4` → `col-md-6` for 2-column).
- **Add items** — Append to the `items` array in the content JSON.
- **Remove items** — Remove from the `items` array; adjust grid if needed.

When reordering, move both the template section and the corresponding JSON block.

## 10. Example Session

```
User: Start a new page called "engineering-program"
Agent: Created scaffold for engineering-program.

User: Add a hero with dark gradient and centered title
Agent: Added HeroStandardApollo with dark theme.
       Current Page: 1. Hero → HeroStandardApollo

User: Add 3 cards showing program tracks
Agent: Added CardImageArticle × 3 in a 3-column grid.
       Current Page: 1. Hero  2. Programs → CardImageArticle × 3

User: Add an FAQ section with 4 questions
Agent: Added OverlapAccordionAtlas with 4 items.
       Current Page: 1. Hero  2. Programs  3. FAQ

User: Insert a stats section before the FAQ showing "10,000+ graduates"
Agent: Inserted SectionStatApollo at position 3.
       Current Page: 1. Hero  2. Programs  3. Stats  4. FAQ

User: Remove the FAQ
Agent: Removed section 4 (FAQ).
       Current Page: 1. Hero  2. Programs  3. Stats

User: Add a standard footer
Agent: Added FooterStandard.
       Current Page: 1. Hero  2. Programs  3. Stats  4. Footer

User: Finalize
Agent: Generated pages/engineering-program.vue and assets/content/engineering-program.json
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/chandima) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->

---
> Source: [tomevault-io/skills-registry](https://github.com/tomevault-io/skills-registry) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-04-29 -->
