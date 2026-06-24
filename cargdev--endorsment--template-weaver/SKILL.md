---
name: template-weaver
description: Creates layout templates (e.g., DashboardLayout, AuthLayout) that act as skeletons where organisms are placed. Uses children/slots pattern.
metadata:
  author: cargdev
---

# Template Weaver

When to use this skill

- Use when you need consistent page scaffolding (headers, sidebars, footers) across pages.
- Triggered by requests to standardize page layouts or to create reusable layout wrappers.

Instructions

1. First Step: Define required regions (header, nav, main, aside, footer) and how children will be injected (React `children` or named slots via props).

2. Second Step: Implement layouts in `src/components/templates/` and ensure they accept a `children` prop and optional slot props (e.g., `sidebar`).

3. Third Step: Use templates in pages under `src/pages/` and provide examples of composition.

Examples

- DashboardLayout:
  `<DashboardLayout sidebar={<ProfileCard/>}><PaperFeed/></DashboardLayout>`

Notes

- Templates should not contain heavy business logic; keep them focused on layout and accessibility (skip links, landmark roles).

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/cargdev) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
