---
name: statistics-ontology-authoring
description: Author and refine formal statistics statement nodes in data/statistics/nodes.json using the Mathematical Statement Node schema. Use when adding or revising theorems, corollaries, definitions, model specifications, assumptions, symbolic forms, or provenance. Use when this capability is needed.
metadata:
  author: displague
---

# Statistics Ontology Authoring

Maintain statistical statements as formal ontology nodes.

## Required Workflow

1. Open `schema/equation-node.schema.json`.
2. Edit `data/statistics/nodes.json` under `statement_nodes`.
3. Keep formal structure complete:
   - `statement_class`
   - `epistemic_status`
   - `theory_context`
   - `formal_statement`
   - `structural_signature`
   - `symbol_lexicon`
   - `semantic_interpretation`
   - `inferential_links`
4. For derived statements, use `statement_class: corollary` and set `inferential_links.entailed_by`.
5. Add a bibliographic provenance entry for new foundational statements.
6. Run `python scripts/validate_nodes.py`.

## Naming and Style

- Use academically standard titles.
- Use mathematically conventional notation in canonical forms.
- Keep inferential claims explicit and minimal.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/displague) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
