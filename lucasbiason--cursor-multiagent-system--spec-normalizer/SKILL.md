---
name: spec-normalizer
description: Padroniza documentação existente no formato canônico Spec-Driven. Remove duplicação e melhora rastreabilidade. Use when this capability is needed.
metadata:
  author: lucasbiason
---

# Spec Normalizer

## Purpose

Normalize, restructure, and clean documentation into the canonical spec format:
- Reduce duplication
- Improve clarity
- Enforce consistent templates
- Add traceability (PRD → FR → AC → Design)

This skill does NOT add new requirements. It reorganizes and clarifies existing information.

## When to Use

- User has arbitrary markdown docs, wiki exports, or old PRDs/specs
- Plan Lead routes "messy docs" input to this skill

## Inputs

- Arbitrary markdown docs, wiki exports, notes
- Old PRDs/specs, technical docs

## Outputs

- Canonical spec folder structure (spec/)
- Updated docs with consistent headings and cross-links

## Rules

1. Preserve meaning. No behavioral changes.
2. If ambiguity exists, annotate instead of rewriting intent.
3. Prefer short, explicit sections with bullets and tables.
4. Add a "Source" note when content was migrated from another doc.
5. If two docs conflict, do NOT resolve—create Open Question(s).

## Process

### Step 0 — Inventory
List docs discovered and categorize: Context, Requirements, Design, Decisions, Runbooks.

### Step 1 — Map to Canonical Structure
Move/merge content into: spec/00-context/, spec/10-requirements/, spec/20-design/, spec/90-decisions/, spec/99-meta/.

### Step 2 — Remove Duplication
If content is duplicated: keep a single source of truth, add links from other places.

### Step 3 — Add Traceability
Add links: PRD goals ↔ FRs; FRs ↔ Acceptance criteria; FRs ↔ Design sections (API/data model).

### Step 4 — Quality Pass
Check for: Vague terms ("fast", "secure", "user-friendly"); Missing definitions (glossary); Missing unknowns; Overly long paragraphs.

## Canonical Structure Reminder

spec/00-context/ | 10-requirements/ | 20-design/ | 30-features/ | 90-decisions/ | 99-meta/

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/lucasbiason) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
