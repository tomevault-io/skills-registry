---
name: documentation-template
description: Produce Diátaxis-organized design system documentation (tutorials, how-to, reference, explanation) with ADRs, Keep-a-Changelog entries, and SemVer-aware contribution guidance. Use when this capability is needed.
metadata:
  author: SwapnilPopat
---
# Documentation Template
A documentation contract that makes the system learnable, searchable, and maintainable — and resilient to ownership change.

## When to Use
- Establishing the docs IA for a new design system or rebooting a stale one.
- Adding a component, pattern, or foundation page that must match house structure.
- Recording a non-trivial design or architectural decision (ADR).
- Releasing a versioned change that affects consumers (changelog + migration).
- Onboarding contributors and reducing review churn with a written contribution guide.

## Stack Baseline (2026)
| Concern | Standard |
| --- | --- |
| Information architecture | Diátaxis (Tutorials / How-to / Reference / Explanation) |
| Component reference | Storybook 8/9 autodocs, MDX2, CSF3 |
| Decision records | MADR 4.0 (Markdown ADR) in `docs/adr/` |
| Changelog | Keep a Changelog 1.1, Conventional Commits |
| Versioning | SemVer 2.0 for the design system package |
| Search / IA tooling | Algolia DocSearch, Pagefind, or built-in Storybook search |
| Accessibility of docs | WCAG 2.2 AA on the docs site itself |
| Status taxonomy | Experimental → Beta → Stable → Deprecated → Removed |

## Prerequisites
- A docs platform (Storybook, Docusaurus, Astro Starlight, or similar) with MDX support.
- Naming convention finalized (file paths, headings, status badges).
- Owner per page (DRI) recorded in frontmatter.
- A token reference and component manifest available to auto-generate sections.

## Instructions
1. **Map every page to a Diátaxis quadrant.** A page does *one* job: teach (tutorial), solve (how-to), describe (reference), or explain (explanation). Don't blend.
2. **Adopt a single page frontmatter contract:**
   ```md
   ---
   title: Button
   status: stable        # experimental | beta | stable | deprecated | removed
   since: 4.2.0
   owner: '@design-systems/forms-pod'
   tags: [forms, action]
   diataxis: reference
   ---
   ```
3. **Component reference page skeleton:**
   ```md
   # Button
   > One-line value statement.
   ## When to use / When not to use
   ## Anatomy
   ## Variants & sizes
   ## Props (auto-generated)
   ## States (default, hover, focus-visible, active, disabled, loading)
   ## Accessibility
   ## Tokens consumed
   ## Content guidelines
   ## Do / Don't
   ## Related components
   ## Changelog
   ```
4. **Pattern page skeleton:** problem → context → solution → anatomy → variants → behavior → good/bad examples → a11y → related patterns → research backing.
5. **Foundation page skeleton:** purpose → principles → rules/specs → examples → exceptions (with rationale) → references.
6. **ADR template (MADR 4):**
   ```md
   # ADR-0042: Adopt OKLCH for color ramps
   - Status: Accepted
   - Date: 2026-02-14
   - Deciders: @design-systems-council
   ## Context
   ## Decision
   ## Consequences
   ## Alternatives considered
   ```
7. **Changelog discipline (Keep a Changelog 1.1):**
   ```md
   ## [5.0.0] - 2026-03-01
   ### Added
   - `Button` `tone="critical"` variant.
   ### Changed
   - **BREAKING**: `Button` prop `kind` renamed to `variant`. Codemod: `npx @org/codemods button-kind-to-variant`.
   ### Deprecated
   - `LegacyDialog` — removal planned for 6.0.0.
   ```
8. **Contribution guide** must spell out: proposal template, design review cadence, code+design review checklists, status-change criteria, release process, and deprecation policy with a minimum two-minor-version notice.
9. **Auto-generate where possible.** Props from TS types, tokens from DTCG JSON, accessibility checklist from axe rules, examples from real Storybook stories.
10. **Freshness audit.** A scheduled job flags pages with `lastReviewed` older than 6 months or whose component version > docs `since`.

## Common Pitfalls
| Pitfall | Why it hurts | Fix |
| --- | --- | --- |
| Mixing tutorial and reference on one page | Readers can't find or trust either | Split per Diátaxis quadrant |
| Hand-maintained props tables | Drift from code immediately | Generate from TS types via Storybook autodocs |
| No status badge on pages | Consumers adopt experimental APIs unknowingly | Mandatory `status` in frontmatter, surfaced in nav |
| Decisions live in Slack/Notion | Lost institutional memory | Capture as ADRs in repo |
| Free-form changelog | Migration becomes guesswork | Enforce Keep a Changelog + Conventional Commits |
| No deprecation timeline | Breaking changes ambush consumers | Policy: deprecate ≥ 2 minor versions before removal, ship codemod |

## Output Format
A docs site organized by Diátaxis quadrants, with: per-page YAML frontmatter (title/status/since/owner/diataxis), an `adr/` folder of MADR records, a root `CHANGELOG.md`, a `CONTRIBUTING.md` describing the proposal → adopt → deprecate workflow, and a freshness dashboard listing stale or version-mismatched pages.

## Authoritative References
- Diátaxis — https://diataxis.fr/
- Keep a Changelog — https://keepachangelog.com/en/1.1.0/
- Semantic Versioning — https://semver.org/
- MADR (Markdown ADR) — https://adr.github.io/madr/
- Storybook docs — https://storybook.js.org/docs
- Conventional Commits — https://www.conventionalcommits.org/

---
> Source: [SwapnilPopat/ai-assistant-skills](https://github.com/SwapnilPopat/ai-assistant-skills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
