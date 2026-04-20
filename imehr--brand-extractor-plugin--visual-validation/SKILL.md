---
name: visual-validation
description: Validate brand extractions by comparing replicated components against originals using a three-layer approach (pixel comparison, structural LLM analysis, token traceability). Implements Gate 5 of the validation pipeline. Use when comparing component replications to originals, performing visual regression testing on design tokens, or validating that extracted tokens accurately reproduce the source design. Use when this capability is needed.
metadata:
  author: imehr
---

# Visual Validation — Gate 5 Three-Layer Comparison

This skill teaches Claude how to perform Gate 5 visual replication validation — the ultimate test of extraction accuracy. If components built from extracted tokens look like the originals, the extraction is correct.

## Three-Layer Validation Architecture

### Layer 1: Automated Pixel Comparison

Uses `pixelmatch` to compute pixel-level similarity between original and replica screenshots.

```bash
python scripts/pixel_compare.py --original ./components/original/ --replica ./components/replica/ --output ./comparison/
```

**Thresholds per component:**

| Component | Criterion ID | Threshold | Rationale |
|---|---|---|---|
| Navigation bar | V-PIX-01 | ≥85% | Complex multi-element, some layout variance acceptable |
| Hero section | V-PIX-02 | ≥80% | Content varies (images, animations), structure matters more |
| Button set | V-PIX-03 | ≥90% | Atomic element, should be near-perfect |
| Card component | V-PIX-04 | ≥85% | Common molecule, tests shadow + spacing + radius |
| Footer | V-PIX-05 | ≥80% | Layout-heavy, content may vary |
| Form elements | V-PIX-06 | ≥85% | Tests input styling, focus states, spacing |

**Overall pass:** Average across all components ≥0.83

### Layer 2: Structural Comparison (LLM Visual Analysis)

You (Claude) visually inspect the original and replica screenshot pairs side by side and evaluate six structural criteria.

**How to evaluate:**
1. View the original component screenshot
2. View the replica component screenshot
3. For each criterion, assess the match quality

**Criteria and scoring:**

| ID | Criterion | What to Check | Score Values |
|---|---|---|---|
| V-STR-01 | Layout fidelity | Column count, alignment, stacking order, spatial arrangement | MATCH (1.0) / CLOSE (0.7) / DIVERGENT (0.3) / MISSING (0.0) |
| V-STR-02 | Colour accuracy | Background, text, accent colours visually match | Same scale |
| V-STR-03 | Typography match | Font, weight, size appear the same | Same scale |
| V-STR-04 | Spacing rhythm | Padding/margin feels consistent | Same scale |
| V-STR-05 | Component completeness | All sub-elements present in replica | Same scale |
| V-STR-06 | Brand impression | Does the replica "feel" like the same brand? | Same scale |

**Evaluation guidelines:**
- MATCH: Virtually indistinguishable at normal viewing distance
- CLOSE: Recognisably the same component, minor differences (slightly different shade, 1–2px spacing)
- DIVERGENT: Same general structure but clearly different appearance
- MISSING: Component or key element absent from replica

**Critical rule:** V-STR-06 (Brand impression) must NOT be MISSING for any component. A MISSING here means the extraction fundamentally failed for that component.

### Layer 3: Token Traceability

For every discrepancy found in Layers 1 and 2, trace back to a specific design token. This is what makes remediation targeted rather than "try again".

**Traceability record format:**

```json
{
  "discrepancy": "Button border-radius in replica (4px) does not match original (8px)",
  "affected_component": "button_primary",
  "affected_token": "borderRadius.md",
  "current_value": "4px",
  "expected_value": "8px",
  "confidence": 0.9,
  "remediation": {
    "action": "UPDATE_TOKEN",
    "token_path": "borderRadius.md.$value",
    "new_value": "8px",
    "requires_re_replication": true
  }
}
```

**Remediation actions:**
- `UPDATE_TOKEN` — Change a token value
- `ADD_TOKEN` — Add a missing token
- `RE_EXTRACT` — Re-run extraction for a specific property
- `RE_REPLICATE` — Rebuild the component with current tokens (code fix, not token fix)

**Priority assignment:**
- HIGH — Discrepancy is visually obvious and affects brand recognition
- MEDIUM — Noticeable on close inspection but does not break brand impression
- LOW — Minor refinement (1px differences, slight shade variations)

## Remediation Loop

When Gate 5 fails:

1. **Parse** all remediation actions from the Layer 3 traceability records
2. **Apply** token updates (UPDATE_TOKEN, ADD_TOKEN)
3. **Re-replicate** ONLY the failed components (not the full set)
4. **Re-capture** screenshots of updated replicas
5. **Re-validate** with all three layers on updated components only
6. **Repeat** up to `max_iterations` times (default 3)

**Circuit breaker:** If max iterations reached and still failing:
- Document the unresolved items with current scores
- Flag severity (HIGH unresolved = recommend human review)
- Continue with document assembly — do not block the deliverable
- Include the discrepancy transparently in the validation report

## Gate 5 Verdict Format

```json
{
  "gate": "GATE_5_VISUAL_REPLICATION",
  "iteration": 1,
  "verdict": "PASS" | "FAIL",
  "layer_1_pixel": {
    "average_similarity": 0.87,
    "threshold": 0.83,
    "components": { ... }
  },
  "layer_2_structural": {
    "components": {
      "nav": {
        "V-STR-01": "MATCH", "V-STR-02": "CLOSE",
        "V-STR-03": "MATCH", "V-STR-04": "CLOSE",
        "V-STR-05": "MATCH", "V-STR-06": "MATCH"
      }
    }
  },
  "layer_3_traceability": [ ... ],
  "pass_conditions": {
    "layer_1_avg_met": true,
    "layer_2_no_missing_brand": true,
    "layer_3_all_high_remediated": true
  },
  "next_action": "PROCEED" | "REMEDIATE_AND_REVALIDATE"
}
```

## Supporting Scripts

- `scripts/pixel_compare.py` — Runs pixelmatch on component pairs, outputs Layer 1 scores

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/imehr) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
