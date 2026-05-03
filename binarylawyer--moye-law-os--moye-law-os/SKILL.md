---
name: moye-law-os
description: Moye Law OS rulebook — Hybrid Mondrian / Kinetic Structuralism for a boutique NY firm. System-level voice, tokens, Practice vs Disciplines IA, and invariants (zero radius, navy borders, hard-offset shadows, kinetic lift/seat, three-voice type, italic-gold, sentence case). Triggers include Moye Law, moye-law-os, Binary Lawyer, Mondrian, or estate / IP / AI law / digital assets / art & entertainment. Defer new UI to moye-law-os-construct; defer audits to moye-law-os-audit. Use when this capability is needed.
metadata:
  author: binarylawyer
---

# Moye Law OS — Design System

A boutique New York legal practice at the intersection of heritage counsel and future-forward technology.

This file is the map. The references carry the depth. Read the reference matching your task before drafting anything substantive — `colors_and_type.css` for tokens, `VOICE.md` for copy, `README.md` for foundations, `practice-vs-disciplines.md` for IA, `patterns/**` for compositions, `components/**` for the component contract.

## Core invariants — do not violate

1. **`border-radius: 0` everywhere.** Zero. Enforced at three layers in v0.3.0: Tailwind `borderRadius.DEFAULT = 0`, an ESLint rule that fails the build on `rounded-(sm|md|lg|xl|2xl|3xl|full)`, and the global reset in `colors_and_type.css`. Do not override.
2. **4px navy borders are the canonical structural weight** (`--bw-structural`). 2px for dense tiles. 6px red top-strap only on Art & Entertainment. 1px hairlines only in footer bottom-bar / analytics disclosure.
3. **Hard-offset shadows only.** `8px 8px 0 var(--navy)` at rest; `4px 4px 0 var(--gold)` on hover of navy buttons; `4px 4px 0 var(--navy)` on hover of white/outline buttons and the discipline-card seat. No blur. No spread.
4. **Kinetic lift on buttons and Practice/Matter cards** — `translate(-2px,-2px)` + hard-offset shadow on hover; `translate(0,0)` + `box-shadow: none` on `:active` (the "lock moment"). **Seat on Discipline cards** — `translate(0.5px, 0.5px)`, shadow tightens `8/8 → 4/4` navy. Opacity-only hover is rejected.
5. **Three-voice type** — **Cormorant Garamond** display, **Inter** body/UI, **JetBrains Mono** eyebrows/labels/metadata. Mono is always `text-transform: uppercase` + `letter-spacing: 0.15em`.
6. **Italic-gold emphasis** on **one word per viewport**. Never more. Never in a button. Never in body copy. Audited via `patches/audits/italic-gold-count.sh`.
7. **Colors** — Navy `#0A2342`, Gold `#C99D56`, Venetian Red `#8B0000`, Engineering Grey `#F3F4F6`. Mondrian accents (`#E31C23 / #0055A4 / #FFD700`) are illustrative only. The editorial v2 palette (`--ink`, `--paper`, `--parchment`, `--wax`, `--gold-deep`) is for document surfaces only.
8. **Icons** — `lucide-react` at stroke 2. Only. No emoji on client surfaces. No Font Awesome, Material, Heroicons.
9. **Copy voice** — quiet, composed, hyper-articulate. Sentence case. No "unlock," "supercharge," "AI-powered," "disrupt." No exclamation points. See `references/VOICE.md`.
10. **Practice vs Disciplines IA split.** The firm's work is organized into two top-level buckets with distinct voice, components, and templates. Never blend them. See `references/practice-vs-disciplines.md`.

## How to consume this folder

- **`references/colors_and_type.css`** — token source of truth. Every color, tint, type variable, border weight, shadow, duration, rhythm, utility class.
- **`references/README.md`** — visual foundations, persona table, iconography, card treatments, imagery rules.
- **`references/VOICE.md`** — copy bible. Includes the v0.3 Practice/Disciplines verb split.
- **`references/practice-vs-disciplines.md`** — the canonical IA split. Read before designing any page that represents the firm's work.
- **`references/patterns/*.md`** — composition patterns: `navbar`, `matter-detail`, `discipline-detail`, `intake-form`, `empty-state`, `loading-state`, `error-state`.
- **`references/components/*.md`** — per-component contracts (generated from repo in Phase 1 of Claude Code rollout). `components/manifest.json` is the index.
- **`references/accessibility.md`** — WCAG 2.2 AA baseline.
- **`patches/`** — Tailwind config, ESLint rule, codemods, and audit scripts for Claude Code to apply to `binarylawyer/moye-law-os`. These are release gates for 0.3.0 → 1.0.0.
- **`ui_kits/website/`, `ui_kits/document/`** — reference HTML for marketing and editorial surfaces. Canonical gallery lives at `/admin/design/**` in the live repo.

## Three personas (copy must map to one)

- **Alex** — founder / innovator · *precision* · IP, digital-asset trusts, cap table
- **David** — HNW patriarch · *permanence* · trusts, multi-generational, concierge
- **Barbara** — family steward · *empathy* · elder law, Medicaid, plain language

## Anti-patterns to catch before shipping

Routine styling defaults produce these. A piece that contains any one of them needs a rewrite, not a patch.

- Any `border-radius` other than 0 — including the sneaky ones (`rounded-sm` in Tailwind, `border-radius: 4px` on an input, pills on chips, circular avatars, rounded focus rings).
- Shadows with blur — `0 4px 6px rgba(0,0,0,0.1)` and its cousins. The house shadow has no second argument.
- Opacity-only hover (`:hover { opacity: 0.8 }`) — always a shadow + translate instead.
- Mondrian primaries (`#E31C23 / #0055A4 / #FFD700`) used as UI chrome — buttons, nav, borders, chips.
- Italic-gold emphasis used twice in one headline, outside display type, or on a button.
- Body copy set in Cormorant; labels or nav set in Cormorant or Inter instead of JetBrains Mono uppercase.
- A logo wordmark, lockup, or icon mark invented to "fill in" for the firm's name.
- Emoji, Font Awesome, Material Icons, Heroicons, or non-lucide iconography.
- Marketing-speak on client surfaces — "unlock," "supercharge," "AI-powered," "disrupt," "game-changing," "seamless," "best-in-class," "trusted partner."
- Exclamation points. Fake metrics ("98% of clients…"). AI apologies ("I apologize," "Certainly").
- Title Case headlines (the house is sentence case).
- Symmetric `6/6` hero splits where a `7/5` asymmetric split belongs.
- Sections without the terminating 4px navy (or gold) bottom border.
- Tailwind `rounded-*` classes anywhere in `src/**`.

## When in doubt

See the "When in doubt — default priorities" section at the end of `references/README.md`. That list belongs next to the visual foundations, not in the invariants block.

## Release criteria — what gates 1.0.0

See `RELEASE-CRITERIA.md`. Summary: patches applied to repo, border-radius lock green in CI, inline-hex drift zero, Kinetic/OpenSesame dedupe complete, italic-gold audit green, intake-form + empty/loading/error patterns implemented, `moye-law-os-audit@0.3.0` runs clean against `main`.

---
> Source: [binarylawyer/moye-law-os](https://github.com/binarylawyer/moye-law-os) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-04-26 -->
