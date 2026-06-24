---
name: papi-verify
description: Verify code against paper. Use when user asks "does this match the paper", "check my implementation", or is implementing equations/algorithms from literature. Use when this capability is needed.
metadata:
  author: hummat
---

# Verify Code Against Paper

You are given:
- Code (or a code excerpt), plus paper excerpts (paste `papi show ... --level ...` output above, or reference exported files).

Project context (optional): $ARGUMENTS

## Rules

- Prefer equations/LaTeX over summaries when there's a conflict.
- If a claim is not supported by provided excerpts, say: "Not supported by provided excerpts."
- For supported claims, include a short quote snippet (<= 15 words) and cite as:
  (paper: <name>, arXiv: <id if present>, source: summary|equations|tex|notes, ref: section/eq/table/figure if present)

## Output

1) **Symbol mapping table**: paper symbol/definition → code variable/tensor (include shapes/units if stated)
2) **Mismatches / risks** (bulleted): missing terms, wrong sign/scale/normalization, shape bugs, hidden assumptions
3) **Fix plan**: minimal code changes (or patch sketch) + suggested asserts/tests to lock correctness

End with:
**Cited papers**: <comma-separated paper names and/or arXiv IDs>

For general CLI commands, see `/papi`.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/hummat) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
