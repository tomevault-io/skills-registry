---
name: atomic-audit
description: Audit a React component (or folder) for Atomic Design correctness and refactor recommendations. Use when components are too large, props are exploding, or responsibilities are unclear. Use when this capability is needed.
metadata:
  author: andireuter
---

# Atomic Design Audit (React)

Audit `$ARGUMENTS` for Atomic Design boundaries.

## What to do

1. Identify the component’s *current* layer (atom/molecule/organism/template/page) and justify.
2. Flag boundary violations:
   - Atom doing fetching/business logic
   - Molecule importing routing
   - Organism hard-coding domain decisions
   - Template binding to real data
   - Page containing large presentational trees
3. Propose a refactor plan:
   - New component split list with target layers
   - Suggested props contracts (data in, events out)
   - Files to create/move and exports to update
4. Provide a minimal diff-style summary of changes (high level, not full patch) and a test plan.

## Output format

- **Findings** (bullet list)
- **Recommended layer** (one line)
- **Refactor plan** (numbered steps)
- **New structure** (tree)
- **Test plan** (checklist)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/andireuter) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
