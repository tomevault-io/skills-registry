---
name: editor-revision
description: Prompt for Editor Eddy to revise a draft document using lexical analysis artifacts Use when this capability is needed.
metadata:
  author: sddevelopment-be
---

# EDITOR REVISION

Prompt for Editor Eddy to revise a draft document using lexical analysis artifacts

## Instructions

Clear context. Bootstrap as Editor Eddy. When ready: 

Refine draft based on lexical analysis outputs.

## Inputs:

- Draft File: \<DRAFT_PATH>
- Lexical Report (path): \<LEX_REPORT>
- Lexical Deltas (path): \<LEX_DELTAS>
- Style Anchor (path): \<STYLE_ANCHOR>
- Preserve Sections (comma headings): \<LOCK_SECTIONS>
- Tone Adjustments (bullets): \<TONE_ADJUST>
- Max Changes (% of lines): \<MAX_CHANGE>
- Output Mode (inline|patch|annotated): \<MODE>

## Task:
1. Parse \<LEX_REPORT> & \<LEX_DELTAS> -> assemble actionable edit set within \<MAX_CHANGE>.
2. Apply ONLY clarity, tone, and cohesion improvements; no new facts.
3. If \<LOCK_SECTIONS> provided: exclude those sections from modification.
4. Produce revised draft to `work/editor/\<slug>-REVISION.md`.
5. Provide rationale summary (top 5 change categories) and line-level patch (if \<MODE>=patch).
6. Generate acceptance checklist (voice preserved, structure intact, no scope drift).

## Output:

- Revised draft content
- Patch or annotated diff
- Rationale summary + checklist

## Constraints:

- Avoid over-polishing into uniform cadence.
- Preserve authorial rhythm and idiosyncratic phrasing when not violating clarity.
- Reject lexical suggestions that introduce hype/flattery.

Ask clarifying questions if \<DRAFT_PATH> or lexical artifacts invalid.

## Workflow

1. Generate acceptance checklist (voice preserved, structure intact, no scope drift).

## Inputs

- **DRAFT_PATH** (required) - Draft File
- **LEX_REPORT** (required) - path
- **LEX_DELTAS** (required) - path
- **STYLE_ANCHOR** (required) - path
- **LOCK_SECTIONS** (required) - comma headings
- **TONE_ADJUST** (required) - bullets
- **MAX_CHANGE** (required) - % of lines
- **MODE** (required) - inline|patch|annotated

## Outputs

- Rationale summary + checklist

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/sddevelopment-be) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
