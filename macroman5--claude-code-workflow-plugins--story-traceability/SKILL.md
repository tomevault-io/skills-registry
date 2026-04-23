---
name: story-traceability
description: Ensure Acceptance Criteria map to Tasks and Tests for PR-per-story workflow Use when this capability is needed.
metadata:
  author: macroman5
---

# Story Traceability

## Purpose
Create a clear AC → Task → Test mapping to guarantee coverage and reviewability.

## Behavior
1. Build a table: AC | Task(s) | Test(s) | Notes.
2. Insert into `USER-STORY.md`; add brief references into each `TASK-*.md`.
3. Call out missing mappings; propose test names.

## Guardrails
- Every AC must have ≥1 task and ≥1 test.
- Keep table compact; link file paths precisely.

## Integration
- Project Manager agent; `/lazy create-feature` output phase.

## Example Prompt
> Add traceability for US-20251027-001.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/macroman5) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
