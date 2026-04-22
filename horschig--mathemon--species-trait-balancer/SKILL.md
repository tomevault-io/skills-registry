---
name: species-trait-balancer
description: Design workflow for creating and balancing player species. Use when defining numerical stats, growth curves, or innate traits for species Resources in `src/classes/species/`. Use when this capability is needed.
metadata:
  author: horschig
---

# Species Trait Balancer

Maintain numerical parity and stylistic uniqueness across the game's species.

## Workflow

1. **Archetype Definition**: Determine the niche (Tank, Scorer, Utility).
2. **Stat Allocation**: Apply the [species-math.md](references/species-math.md) framework to ensure point-buy parity (~100 pts total).
3. **Trait Synergy**:
   - Pair an active ability with a governing stat (e.g., "Strength-based Slam").
   - Balance power traits with corresponding penalties.
4. **Verification**: Link to `BaseSpecies.gd` and verify the `.tres` format.

## Guidelines
- **Lore Parity**: Cross-reference description with the world-building notes in `docs/context/`.
- **A/B Testing**: Always compare against the "Human" baseline.

## Artefacts to Update (when changing species)

- Update `src/classes/species/` resource files (`.tres`) and add the change to `./docs/todo/master_todo.md` under the related story (e.g., `US-AI-010`).
- Add or update unit tests in `./tests/unit/` that cover stat boundaries or behavior changes.
- If descriptive text or lore references change, update `./.github/skills/obsidian-narrative-vault/references/lore-standards.md` and notify the PM via the master todo.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/horschig) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
