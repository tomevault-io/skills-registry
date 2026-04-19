---
name: ciso-policy
description: > Use when this capability is needed.
metadata:
  author: iy3r
---

# /ciso-policy — Targeted CSCRF Policy Generator

Generate one policy area at a time unless the user explicitly asks for multiple.

## Quality Gates

- Verify periodicities against Table 15 for every review cycle mentioned.
- Verify reporting authorities against Tables 16-23 for the entity type.

## Performance Notes

- Check EVERY mandatory guideline for the entity's tags — do not sample or skip under context pressure.
- Cite specific guideline IDs (e.g., PR.AA.G1) for every policy statement.
- Prefer verbatim CSCRF text over paraphrasing when referencing requirements.

## Context Rules

- Never bulk-load the framework.
- Use `./.claude/skills/ciso-policy/references/policy-area-map.json` as the only source for area routing.
- Use `./.claude/skills/ciso-policy/references/load-matrix.md` for context-budget guardrails.
- Always read `./.claude/skills/ciso-policy/references/policy-templates.md` before drafting.

## Portability and Fallback

- Default path: run Task using local contracts:
  - `./.claude/skills/ciso-policy/references/agents/policy-drafter.md`
  - `./.claude/skills/ciso-policy/references/agents/reviewer.md`
- Optional optimization: if named specialist agents (`cscrf-policy-drafter`, `cscrf-reviewer`)
  are available, use them with the same input/output contracts.
- Fallback path (Task unavailable): draft inline, then run a separate inline QA pass with the
  same reviewer checklist before writing final output.
- Mark final summary with `mode: task` or `mode: inline-fallback`.

## Inputs

- Policy area slug (from `policy-area-map.json`) or `all`
- Entity category
- Entity name
- Optional context: tech stack, team size, constraints

## Argument Parsing

The user invokes this as: `/ciso-policy [policy-area] [entity-category] [entity-name]`

**Entity categories:** `mii`, `qualified`, `mid-size`, `small-size`, `self-certification`

**Entity name:** Optional. If omitted, use `[Your Entity Name]`.

### If arguments are missing or unclear

Ask using AskUserQuestion:

1. If no policy area specified, ask:
   "Which policy would you like to generate?" with options:
   - Cybersecurity Policy (Recommended)
   - Access Control Policy
   - Incident Response Plan
   - All policies
2. If no entity category specified, ask:
   "What is your SEBI RE category?" with options: MII, Qualified, Mid-size, Small-size, Self-certification

## Workflow

1. Parse arguments and collect missing context.
2. Read `policy-area-map.json`.
3. Resolve requested area:
- If slug matches `areas[].slug`, use it.
- Else if it matches `title`/`aliases`, map to slug.
- Else ask user to choose a valid area.
4. Build area list:
- Single area: one-item list.
- `all`: all slugs from map order.
5. For each area:
- Task -> policy drafter contract (`references/agents/policy-drafter.md`) with area + entity inputs + output path.
- Task -> reviewer contract (`references/agents/reviewer.md`) for traceability and periodicity QA.
- If review fails, patch and re-run one review pass.
6. Write each file to `docs/policies/[slug].md`.
7. For multi-area runs, write `docs/policies/README.md` index.

### For `all`

Process each area as an independent draft/review cycle. Do not draft all areas in one pass.

## Examples

### Example 1: Single policy for a qualified RE

User says: `/ciso-policy access-control qualified "Acme Securities Ltd"`

Actions:
1. Read policy templates.
2. Read policy-area map and resolve `access-control`.
3. Load mapped framework files.
4. Task -> cscrf-policy-drafter.
5. Task -> cscrf-reviewer.
6. Write `docs/policies/access-control.md`.

### Example 2: Generate all policies for a mid-size broker

User says: `/ciso-policy all mid-size "Zenith Broking Pvt Ltd"`

Actions:
1. Read policy-area map.
2. Iterate all slugs in map order.
3. Per area: draft -> review -> fix if needed -> write.
4. Write `docs/policies/README.md` index.

### Example 3: Incident response without entity name

User says: `generate incident-response plan`

Actions:
1. Ask for entity category.
2. Use `[Your Entity Name]` placeholder.
3. Resolve and load framework files via policy-area map.
4. Task -> cscrf-policy-drafter and cscrf-reviewer.
5. Write `docs/policies/incident-response.md`.

## Common Issues

### Policy area keyword not recognized
If a free-text policy name is provided, match against `title` and `aliases` in the area map.
If no match, ask the user to choose a valid area. Do not invent framework mappings.

### Framework file missing for requested area
If any `framework_files` path in the area map is missing, report the gap and skip that area.
Do not draft policy content without source framework files.

### Reviewer rejects the draft
If `cscrf-reviewer` rejects the draft, apply targeted fixes and re-run one review pass.
If the second review also fails, present the draft with findings and let the user decide.

### `all` for large entities
Generating all policies for an MII may produce substantial output. Process one area at a time
and write each file before starting the next.

### docs/policies/ directory missing
Create it before writing the first policy file. Do not fail silently.

## Failure Envelope (Standardized)

For any area-level failure, use this contract:

1. Retry budget: one retry after fixing concrete mapping/context errors.
2. Persist partial output:
   - `docs/.ciso-work/policy-[area]-partial.md`
   - `docs/.ciso-work/policy-[area]-error.json` with `area`, `error`, `attempts`, `next_actions`
3. Terminal failure response must include:
   - policy area(s) failed,
   - root cause with mapped file/tool context,
   - completed policy areas (if any),
   - exact next user decision needed.

## Key Reminders

- Use verbatim framework references for cited requirements.
- Scale policy depth to entity size using `policy-templates.md` targets.
- Include periodicities from Table 15 (`meta/compliance.md`).
- Set correct reporting authority via Tables 16-23 (`meta/compliance.md`).

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/iy3r) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
