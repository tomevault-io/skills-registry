---
name: create-issue-card-execplan
description: Create a new junior-proof issue card plus ExecPlan in this repo, aligned with AGENTS.md, PLANS.md, and issues/ISSUE_CARD_TEMPLATE.md. Use when asked to draft/spec a bugfix/feature/refactor with explicit requirements, file paths, concrete steps, tests (red-green), acceptance criteria, verification, risks, and rollback. Use when this capability is needed.
metadata:
  author: eestevanell
---

# Create Issue Card + ExecPlan

## Purpose

Draft an issue card and (when non-trivial) an ExecPlan that a complete novice can implement end-to-end with no ambiguity, no missing steps, and minimal regression risk. Optimize for correctness, maintainability, and low blast radius.

## Inputs (required)

- A brief describing the work (bug/feature/refactor), including desired user-visible outcome.

If the brief is missing critical details, ask only the minimum clarifying questions needed to eliminate ambiguity (see “Minimum clarifying questions”).

## Minimum clarifying questions (ask only if needed)

1. What is the exact user-visible outcome (one sentence), and how do we see it working?
2. What is broken/missing today (current behavior), and how do we reproduce/observe it (URLs, API routes, inputs)?
3. Any hard constraints (must not change X, must keep contract Y, deadline/risk sensitivity)?

If the user cannot answer (or for speed), resolve by investigating the repo and recording evidence in the card.

## Procedure

1. Read repo rules and templates:
   - `AGENTS.md`
   - `PLANS.md`
   - `issues/README.md`
   - `issues/ISSUE_CARD_TEMPLATE.md`
2. Pick filenames (keep consistent with repo conventions):
   - Issue card (preferred once numbered): `issues/open/<number>-<type>-<kebab-summary>.md`
   - ExecPlan: `issues/execPlans/<number>-<type>-<kebab-summary>_execplan.md`
   - If blocked on a number, use `issues/open/draft-<type>-<kebab-summary>.md` and `issues/execPlans/draft-<type>-<kebab-summary>_execplan.md`, and note in both files that a rename is required after `gh md-issues push`.
3. Investigate prior art (do not guess):
   - Search `issues/**` for similar work.
   - Identify the likely module(s) and exact file paths.
   - Capture concrete evidence (errors, logs, stack traces, screenshots, failing test output) as text in the issue card.
4. Draft the issue card (authoritative spec):
   - Start from `issues/ISSUE_CARD_TEMPLATE.md`; keep headings intact.
   - Fill every section; use “None.” instead of leaving blanks.
   - Include exact strings/contracts when they matter (route paths, error messages, queue names, status values).
   - Make blast radius explicit in `Expected Files/Areas` and call out high-risk files (e.g., `Program.cs`) with rationale.
   - Set `## Status` to `Proposed.` and keep YAML frontmatter accurate (`title`, `labels`, `state`).
   - Reference the ExecPlan path at the top (`ExecPlan: issues/execPlans/<slug>_execplan.md`).
5. Decide whether an ExecPlan is required:
   - Required when the change is complex, cross-cutting, risky, or spans multiple modules (default to “yes” if unsure).
   - For tiny/local changes, the card can stand alone; explicitly justify why an ExecPlan is unnecessary.
6. Draft the ExecPlan (novice-executable spec):
   - Follow `PLANS.md` strictly.
   - The ExecPlan file should contain only the plan (no outer triple-backtick fence).
   - Never use nested triple-backtick fences inside the plan; use indented snippets for commands/transcripts/code.
   - Define every non-obvious term in `Context and Orientation` (if you can’t define it, don’t use it).
   - Make TDD explicit: name the exact test file(s), test name(s), and the red/green transition.
   - Include explicit file paths and insertion points (type/method/component/template names).
   - When adding/changing interfaces/contracts, include an `Interfaces and Dependencies` section with explicit signatures and dependencies.
   - Prefer the simplest design that meets requirements; when multiple approaches exist, pick the best maintainability-to-risk ratio and record the rationale in `Decision Log`.
   - Reuse repo patterns and centralize shared literals (queue names, log templates, status codes, error strings) in the module-appropriate location instead of duplicating strings.
   - Include short indented snippets for the most error-prone bits (contracts, signatures, route strings, example test cases, expected outputs).
   - Include concrete commands and expected outputs (short transcripts) for build/tests.
   - Include rollback/recovery and idempotence guidance.
7. Junior-proof quality gate (before finalizing files):
   - Remove any “TBD”, “should”, “maybe”, “etc.” where it hides a decision.
   - Ensure every requirement maps to an acceptance criterion and a verification step.
   - Ensure no hidden knowledge: a new engineer can run the listed commands and recognize success/failure.
   - Ensure the plan does not smuggle implementation via “follow existing patterns” without pointing to the exact pattern file(s).

## Output

- Create/update a new issue card under `issues/open/` (preferred once an issue number exists) or a draft path when blocked on numbering.
- Create/update the ExecPlan under `issues/execPlans/`.

Use the templates in:
- `references/issue_card_skeleton.md`
- `references/execplan_skeleton.md`
- `references/junior_proof_gate.md`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/eestevanell) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
