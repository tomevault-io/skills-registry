---
name: outline-wiki-content
description: Draft, restructure, and maintain documentation for Outline-based wikis and knowledge bases. Use when requests involve Outline collections or docs, article outlines, markdown-to-Outline adaptation, rewriting for clarity, content audits, or documentation governance such as owners, review cadence, and deprecation notes. Use when this capability is needed.
metadata:
  author: janhoon
---

# Outline Wiki Content

Use this skill to turn rough knowledge into clean, reliable, Outline-ready documentation and to keep existing wiki content coherent over time.

## Workflow

1. Scope the job.
   - Identify mode: create, revise, reorganize, or audit.
   - Capture audience, collection, source material, and success criteria.
   - List assumptions explicitly when requirements are incomplete.

2. Choose the output shape.
   - Deliver a full markdown draft for new pages.
   - Deliver a targeted patch list for updates to existing pages.
   - Deliver a collection map plus migration plan for restructures.
   - Deliver a prioritized findings table for audits.

3. Build content for scanability and maintenance.
   - Keep one clear title and a short purpose statement at the top.
   - Use `##` sections for major topics; use `###` only when needed.
   - Prefer numbered steps for procedures and bullets for reference data.
   - Include prerequisites, expected result, and troubleshooting for task docs.
   - End operational docs with ownership and review cadence.

4. Validate before finalizing.
   - Verify factual consistency against provided sources.
   - Remove duplicated guidance and resolve contradictory statements.
   - Flag unknowns instead of inventing policy or system behavior.

## Task Modes

### Create or Rewrite Page

- Draft a compact outline before writing full prose.
- Start with context and outcome; then provide actionable details.
- Add examples, commands, and decision criteria where ambiguity is likely.
- Keep section titles task-oriented (for example: "Restore Access" not "Access").

### Update Existing Page

- Preserve stable concepts and links unless a change is requested.
- Show changes as: `keep`, `update`, `remove`, `add`.
- Highlight breaking changes, renamed terms, and follow-up edits needed in related docs.

### Reorganize Collection

- Propose a target tree grouped by user intent, not by internal team structure.
- Keep depth shallow where possible (typically 2-3 levels).
- Define document ownership per top-level area.
- Include a migration sequence: move, merge, redirect, archive.

### Audit Documentation Quality

- Score pages on clarity, correctness, freshness, and findability.
- Mark stale pages with an explicit review or archive recommendation.
- Recommend a small number of high-impact fixes first.

## Output Templates

### New Page Draft

Use this structure:

```markdown
# <Title>

<1-2 sentence purpose and scope>

## When to Use This
## Prerequisites
## Steps
## Validation
## Troubleshooting
## Ownership and Review
```

### Update Proposal

Use this structure:

```markdown
Document: <collection/path/title>

## Keep
- ...

## Update
- ...

## Remove
- ...

## Add
- ...
```

## Reference Material

- Read `references/api_reference.md` when interacting with Outline APIs, IDs, or automation.
- Stay in this file for purely editorial writing and information architecture tasks.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/janhoon) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
