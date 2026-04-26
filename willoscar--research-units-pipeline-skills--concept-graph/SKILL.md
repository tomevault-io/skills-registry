---
name: concept-graph
description: | Use when this capability is needed.
metadata:
  author: willoscar
---

# Concept Graph (prerequisites)

Goal: represent tutorial concepts as a prerequisite DAG so modules can be planned and ordered.

## Inputs

- `output/TUTORIAL_SPEC.md`

## Outputs

- `outline/concept_graph.yml`

## Output schema (recommended)

A minimal, readable YAML schema:

- `nodes`: list of `{id, title, summary}`
- `edges`: list of `{from, to}` meaning `from` is a prerequisite of `to`

Constraints:
- Graph should be acyclic (DAG).
- Prefer 10–30 nodes for a medium tutorial.

## Workflow

1. Read `output/TUTORIAL_SPEC.md` and extract the concept list implied by objectives + running example.
2. Normalize each concept into a node with a stable `id`.
3. Add prerequisite edges and verify the graph is acyclic.
4. Write `outline/concept_graph.yml`.

## Definition of Done

- [ ] `outline/concept_graph.yml` exists and is a DAG.
- [ ] Nodes cover all learning objectives from `output/TUTORIAL_SPEC.md`.
- [ ] Node titles are specific (not “misc”).

## Troubleshooting

### Issue: the graph looks like a linear list

**Fix**:
- Add intermediate prerequisites explicitly (e.g., “data model” before “evaluation protocol”).

### Issue: cycles appear (A → B → A)

**Fix**:
- Split concepts or redefine edges so prerequisites flow in one direction.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/willoscar) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
