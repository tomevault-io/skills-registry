---
name: tidy-first
description: Apply Tidy First code cleanup techniques and sequencing before behavior changes. Use when users ask to organize or code cleanup, or when a plan from create-plan is provided and the work needs tidying in the code areas that plan will touch. Use when this capability is needed.
metadata:
  author: mvernacc
---

# Tidy First

## Overview
Use this skill to apply small, safe tidyings in the exact code areas a plan will touch, then execute the plan. Keep tidying and behavior changes separate and small.

## Workflow

1. Confirm the plan
- Require or reference a plan created with the create-plan skill.
- If no plan is provided, ask for it before changing code.

2. Map plan to code
- Identify files, modules, and functions the plan will touch.
- Use search to locate call sites and data flows the plan will modify.

3. Tidy first, locally
- Tidy only in the identified areas to make the upcoming change easier to read and modify.
- Choose tidyings from `references/tidy-first-summary.md` based on what you see.
- Keep each tidying step tiny and behavior-preserving; stop when the behavior change is easy.

4. Separate tidyings from behavior changes
- Keep tidyings in separate commits/PRs or clearly separated patches.
- Do not mix structural changes with behavior changes in the same edit unless the user insists.

5. Execute the plan
- Follow the plan steps in order after tidying.
- If more tidying is needed mid-change, pause and create a new tidy-only step before continuing.

6. Decide after/later/never
- If you discover other messes, use the timing guide in the reference and keep a short list of later tidyings.

## Guidance

- Bias toward tidying first when it immediately reduces change cost or improves understanding.
- Avoid speculative, wide-scope cleanup; keep the scope to what the plan will touch.
- If you get tangled (tidying and behavior changes mixed), consider reverting and redoing with a clean sequence.

## References
- Use `references/tidy-first-summary.md` for the tidyings catalog and decision guide.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mvernacc) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
