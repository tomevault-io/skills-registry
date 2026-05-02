---
name: plans-and-specs
description: |- Use when this capability is needed.
metadata:
  author: igorwarzocha
---

# Plans and Specs

<objective>
Plans are actionable work breakdowns stored as markdown with frontmatter. Specs are reusable requirements/documents. Plans link to specs via appendSpec. readPlan expands linked specs inline.
</objective>

<rules>
After createPlan: MUST call appendSpec for each REPO scope spec (sequential), then ask about FEATURE specs.
Before major work: MUST use readPlan to get plan + all linked specs.
appendSpec: MUST be sequential calls, never batch/parallel.
Specs MUST exist before appendSpec.
markPlanDone: MUST ensure plan fully completed first.
</rules>

<procedure>

## Planning Workflow

1. Check if plan already exists - if exact match, execute instead of creating
2. createPlan with name, idea, description (3-5 words), steps (min 5)
3. For each REPO spec returned: appendSpec(planName, specName) - sequential
4. Ask user: "Want a FEATURE spec for this plan?"
5. If yes: createSpec, then appendSpec
6. Before work: readPlan to get full context

## Spec Creation

createSpec with name, scope (repo/feature), reusable content.

## Completion

markPlanDone only after all work verified complete.

</procedure>

<errors>

Invalid name: Use [A-Za-z0-9-], max 3 words.
Description error: 3-5 words, not overlapping name.
Steps error: Need min 5 specific steps.
Already exists: Use different name.
Spec not found: Create spec first.
Concurrent updates: appendSpec must be sequential.

</errors>

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/igorwarzocha) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
