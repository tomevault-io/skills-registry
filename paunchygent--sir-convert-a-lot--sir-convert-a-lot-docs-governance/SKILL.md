---
name: sir-convert-a-lot-docs-governance
description: >- Use when this capability is needed.
metadata:
  author: paunchygent
---

# Sir Convert-a-Lot Docs Governance

## Use This Skill When

- Planning or scope changes are introduced.
- Docs/rules/frontmatter contracts need creation or repair.
- Backlog structure, templates, or indexing workflows are updated.

## Canonical Structure

- Planning: `docs/backlog/` with `programme -> epic -> story -> task`.
- Decisions: `docs/decisions/`.
- Product direction: `docs/pdr/`.
- Operations: `docs/runbooks/`.
- Research/reviews/roadmaps: `docs/reference/`.
- Contract source: `docs/_meta/docs-contract.yaml`.

## Non-Negotiables

- Frontmatter title is canonical; do not add competing markdown H1 headings.
- Use template-driven creation scripts for new items.
- Keep task units PR-sized; story linkage is optional but explicit when present.

## Canonical Commands

```bash
pdm run new-programme "<title>"
pdm run new-epic "<title>"
pdm run new-story "<title>"
pdm run new-task "<title>"
pdm run new-review "<title>"
pdm run new-doc converters/<name>.md --title "<Title>"
```

```bash
pdm run validate-tasks
pdm run validate-docs
pdm run index-tasks --root "$(pwd)/docs/backlog" --out "/tmp/sir_tasks_index.md" --fail-on-missing
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/paunchygent) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
