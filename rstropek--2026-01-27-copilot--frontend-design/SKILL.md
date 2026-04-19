---
name: frontend-design
description: Best practices for frontend design, including layout, styling, and user experience considerations. Use when this capability is needed.
metadata:
  author: rstropek
---

This skill guides creation of distinctive, production-grade frontend interfaces that follow our established design principles and best practices.

## Fonts

- Use sans-serif fonts for better readability on screens.
- Maintain consistent font sizes and weights across the application.
- Use relative units (em, rem) for font sizes to ensure scalability.
- Ensure sufficient contrast between text and background for accessibility.
- Limit the number of different fonts used to maintain visual coherence.

## Colors

* Text color:
  - #222 on light backgrounds
  - #fff on dark backgrounds (e.g. #222 background)
* Highlight color: Shades of yellow
  - #ffde00;
  - #776800;
  - #bba300;
  - #ffe84c;
  - #fff299;
  - #fffbe5;
* Inactive background color: Shades of gray
  - #eee
  - #f4f4f4

## Layout

- Use a maximum content width of 1280px, centered horizontally on the page.
- Design for desktop (full HD resolution) only; responsive design is not required.
- Use CSS Grid and Flexbox for layout structures.
- Maintain consistent spacing and alignment throughout the interface.
- Use whitespace effectively to enhance readability and focus.

## Silo Icon

The UI contains silo placeholders and icons in various places. The codebase contains a `silo.svg` in the `public` folder that should be used as the default silo icon/placeholder wherever a silo icon is needed.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/rstropek) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
