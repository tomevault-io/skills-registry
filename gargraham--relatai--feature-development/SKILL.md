---
name: feature-development
description: Implement new features or bugfixes with a clear plan and verification steps. Use when this capability is needed.
metadata:
  author: gargraham
---
# Skill: Feature Development

> See also: `AGENT_SYSTEM.md`

## Phase
- Change

## Purpose
- Deliver a feature or bugfix with minimal fuss and a clear Proof of Life.

## Use When
- “Add X”
- “Implement Y”
- “Fix bug Z”
- “Make it do ___”

## Inputs to Request (only if blocking)
- One-sentence goal
- Out-of-scope (if needed)
- Proof of Life expectation (test/URL/curl/UI step)

## Workflow
- Clarify:
  - goal (1 sentence)
  - out-of-scope (bullets)
- Plan (right-sized):
  - Small: 2–4 bullets
  - Medium/Large: 5–10 bullets + use SCRATCHPAD.md
- Implement:
  - keep diffs tight
  - avoid unrelated refactors
- Proof of Life:
  - provide exact command/URL/curl/UI steps
- Wrap-up:
  - summarize changes
  - list verification steps
  - note follow-ups

## Output
- **Goal**
- **Plan** (bullets)
- **Files changed**
- **Proof of Life**
- **Follow-ups** (optional)

## Memory
- Append one line to JOURNAL.md (what changed + where you left off)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/gargraham) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
