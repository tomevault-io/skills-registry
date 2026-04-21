---
name: progress
description: Display and manage workflow progress for site generation from Stitch designs Use when this capability is needed.
metadata:
  author: khaym
---

# Progress Skill

Display and manage the standard workflow progress for generating a website from Google Stitch designs.

## Progress File

Progress state is persisted in `progress.md` at the project root (generated site repository).
If the file does not exist, create it from the template below on first invocation.

### Template

```markdown
# Project Progress

## Steps

- [ ] 1. Initial Setup & Hearing — Environment setup and requirements gathering
- [ ] 2. Project Scaffold — Generate minimal Astro project structure
- [ ] 3. Stitch Data Fetch — Fetch design mock data via stitch-cache skill
- [ ] 4. Design Tokens — Generate global.css from Stitch data
- [ ] 5. Layout & Components — Implement layouts and shared components
- [ ] 6. Page Implementation — Build pages from Stitch screen data
- [ ] 7. Favicon — Generate favicon from site name and accent color
- [ ] 8. Review & Polish — User review, accessibility check, refinements
- [ ] 9. Deploy — Deploy to Cloudflare Pages (optional)

## Notes

(User requirements and decisions are recorded here)
```

## Behavior

1. Read `progress.md` (create from template if missing)
2. Display current status to the user:
   - Show all steps with completion status
   - Highlight the current (first incomplete) step
   - Show any recorded notes
3. If the user requests a status change, update the checkbox accordingly
4. Never skip steps — warn the user if they attempt to mark a later step complete while earlier steps are incomplete

## Step Completion Criteria

| Step | Complete when |
|------|--------------|
| 1. Initial Setup & Hearing | `/setup` skill completed; environment verified and hearing results recorded in Notes |
| 2. Project Scaffold | `/scaffold` skill completed; Astro project builds without errors |
| 3. Stitch Data Fetch | `/stitch-cache` skill has run; `docs/stitch/reference.md` exists |
| 4. Design Tokens | `src/styles/global.css` contains design tokens derived from Stitch data |
| 5. Layout & Components | Base layout and shared components are implemented |
| 6. Page Implementation | All pages from Stitch screens are implemented |
| 7. Favicon | `public/favicon.svg` exists and `BaseLayout.astro` references it |
| 8. Review & Polish | User has confirmed the site looks correct |
| 9. Deploy | Site is deployed or user has opted out |

## Step 7: Favicon Details

Generate the favicon before asking the user to review the site.

1. Ask the user what they want for the favicon (e.g., first letter of site name, a simple icon, or a custom image they already have)
2. Read the accent color from `src/styles/global.css` (`--color-accent`) and site name from `progress.md`
3. Generate `public/favicon.svg` based on the user's choice — for example, a rounded square with the accent color and the first letter of the site name
4. Verify `<link rel="icon">` in `BaseLayout.astro` points to the favicon

## Step 8: Review & Polish Details

After the favicon is in place, ask the user to review the complete site:

- Start the dev server if not already running
- Ask the user to check each page in the browser (including the favicon in the browser tab)
- Address any feedback (layout, colors, spacing, text)

---

## Notes Management

- When the user provides requirements or makes decisions, append them under the `## Notes` section
- Format: `- [YYYY-MM-DD] <content>`
- Notes are a living document — the main session references them to maintain context

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/khaym) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
