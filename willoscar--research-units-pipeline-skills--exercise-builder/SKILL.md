---
name: exercise-builder
description: | Use when this capability is needed.
metadata:
  author: willoscar
---

# Exercise Builder

Goal: attach at least one verifiable exercise to every module so the tutorial has a teaching loop.

## Inputs

- `outline/module_plan.yml`

## Outputs

- Updated `outline/module_plan.yml`

## Exercise schema (recommended)

For each module, add an `exercises` list. Each exercise should contain:
- `prompt`
- `expected_output`
- `verification_steps` (a checklist)

## Workflow

1. Read `outline/module_plan.yml` and enumerate modules.
2. For each module, design ≥1 exercise that directly verifies the module objectives.
3. Ensure every exercise has an expected output and a verification checklist.
4. Update `outline/module_plan.yml` in place.

## Definition of Done

- [ ] Every module in `outline/module_plan.yml` has ≥1 exercise.
- [ ] Every exercise includes `expected_output` + `verification_steps`.

## Troubleshooting

### Issue: exercises are open-ended with no verification

**Fix**:
- Convert them into “do X → observe Y → verify Z” with concrete artifacts.

### Issue: exercises drift from the running example

**Fix**:
- Re-anchor each exercise to the module’s `running_example_steps` so the tutorial stays coherent.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/willoscar) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
