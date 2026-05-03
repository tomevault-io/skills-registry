---
name: doc-steward
description: > Use when this capability is needed.
metadata:
  author: joshka
---

# Doc Steward

## Overview

Plan, write, and maintain high-signal documentation that stays correct as code evolves.

## Non-negotiables

- Treat docs (including docstrings/comments) as part of the public API.
- Prefer "why / constraints / invariants / edge cases" over restating implementation.
- Never invent rationale. If intent is not provable from code/tests/usages:
  - ask one concise question, or
  - write a TODO that states what is unknown and what evidence is needed.
- Keep `Unknown` usage minimal and bounded. Prefer resolving to `Confirmed` before finishing a pass.
- When providing multiple follow-up items, always use numbered lists for easy user response.
- Fix doc drift in the same diff as behavior change.
- Keep behavior unchanged unless the user explicitly asks for code changes.
- In `full` passes, default to deep docs for modules, types, and functions in scope.
- Avoid low-value docs that only restate names or signatures.

## Goal Picker (Default)

If the user does not pick a mode, ask one short question:

- "What do you want to achieve?"

Offer these options so the user can choose a mode. Include the short "use when" and "includes"
guidance so they can pick without guessing:

1. `Ratchet Mode`: improve existing docs while keeping tone.
   Use when docs already exist and mostly work. Includes targeted clarity/correctness edits and
   drift fixes.
1. `PR Narration Mode`: explain and review a PR.
   Use during author/reviewer handoff. Includes PR narrative, contracts/invariants, doc deltas,
   and review risks.
1. `Bootstrap Mode`: create first docs for a new project.
   Use when docs are minimal/absent and speed matters. Includes README, quick start, usage, config,
   and basic troubleshooting.
1. `Greenfield Full Coverage Approach`: fully document a new app, including private methods.
   Use when you want deep docs now, not later. Includes repo-wide module/type/function docs,
   depth checks, and coverage ledger with thresholds.
1. `Docstring / Rustdoc Mode`: update API docs/docstrings.
   Use when behavior or interfaces changed. Includes contract-focused doc updates and alignment with
   tests/call sites.
1. `Recovery Mode`: repair mixed-quality docs.
   Use when docs are inconsistent or stale. Includes audit, prioritized backlog, and first
   remediation batch.
1. `Bulk Update Mode`: run a broad consistency pass.
   Use for large standardization passes. Includes terminology/style normalization and unresolved
   drift report.
1. `Process Improvement Mode`: improve repo-wide docs process/standards.
   Use when defining team/repo defaults. Includes AGENTS.md guidance, lint standards, and adoption
   checklist.

Bootstrap vs Greenfield (explicit distinction):

- `Bootstrap Mode`: establish minimum usable docs quickly for onboarding and day-one usage.
- `Greenfield Full Coverage Approach`: produce deep, broad documentation coverage early, including
  private methods/functions and coverage gates.

Optional context (skip any item to use defaults):

Scope (default `touched-files`): `PR` changed files only; `files` exact files you list; `module`
one subsystem across related files; `path` everything under a folder path.

Audience (default `maintainer`): `maintainer` future code maintainers; `reviewer` PR reviewers
with limited context; `operator` people running/troubleshooting the system; `user` end users or
contributors following setup/usage docs.

Constraints (default repo style and lint rules): `style rules` preserve local
voice/terminology/conventions; `lint requirements` enforce configured lint checks (for example
`markdownlint-cli2`).

Prompt format after mode selection (required for clarity):

1. Ask for profile in one line:
   `Optional profile: reply with 'owner, repo-wide, full, preserve-local' or 'use defaults'.`
1. Immediately state effective defaults for the selected mode:
   `Effective defaults for this mode = <role>, <reach>, <depth>, <style-authority>.`
1. If mode preset overrides baseline defaults, state that explicitly:
   `This mode overrides baseline defaults.`
1. If the user replies `use defaults`, proceed without further profile questions.
1. Avoid vague phrasing like "I’ll apply defaults" without listing the actual defaults.

Mode preset defaults:

1. `Ratchet Mode`: `contributor`, `touched-files`, `quick`, `preserve-local`.
1. `PR Narration Mode`: `contributor`, `touched-files`, `quick`, `preserve-local`.
1. `Bootstrap Mode`: `contributor`, `touched-files`, `quick`, `preserve-local`.
1. `Greenfield Full Coverage Approach`: `owner`, `repo-wide`, `full`, `preserve-local`.
1. `Docstring / Rustdoc Mode`: `contributor`, `touched-files`, `quick`, `preserve-local`.
1. `Recovery Mode`: `contributor`, `touched-files`, `quick`, `preserve-local`.
1. `Bulk Update Mode`: `owner`, `repo-wide`, `full`, `preserve-local`.
1. `Process Improvement Mode`: `owner`, `repo-wide`, `quick`, `preserve-local`.

## Session Calibration (First Run In A Repo)

When repo usage style is unclear, ask short setup questions before drafting:

1. Role: `contributor` or `owner`.
1. Reach: `touched-files` or `repo-wide`.
1. Output depth: `quick` or `full`.
1. Style authority: `preserve-local` or `normalize-lightly`.

Baseline defaults (used when a mode has no preset override):

- Role: `contributor`
- Reach: `touched-files`
- Output depth: `quick`
- Style authority: `preserve-local`

Behavior implications:

- `contributor` + `touched-files`: keep recommendations scoped to the active change.
- `owner` + `repo-wide`: include backlog, prioritization, and consistency controls.
- `quick`: deliver concise actionable output only.
- `full`: include rationale, alternatives, and follow-up suggestions.

If this profile cannot be persisted, end with a reusable one-line invocation (see
`references/quick-modes.md`).

## Unknown Control

Use an explicit unknown budget:

- `touched-files` + `quick`: target 0-3 `Unknown` items.
- `repo-wide` or `full`: target 0-10 `Unknown` items.
- If the pass exceeds budget, prioritize resolving highest-impact unknowns first.

Required end-of-pass confirmation:

1. List remaining `Unknown` items as numbered bullets with file/section context.
1. Ask for a single confirmation reply:
   - "Confirm whether to keep all remaining Unknowns, or list item numbers to resolve now."
1. If user confirms acceptance, proceed and record unknowns as accepted for this pass.

## Doc Depth Standard (Modules, Types, Functions)

Use this depth policy in `full` passes and in Greenfield flows.

For each module in scope:

- Purpose and boundaries (what belongs here and what does not).
- Key concepts/terminology and how they connect to neighboring modules.
- Invariants and safety/ordering constraints.

For each public type in scope:

- Role in the system and lifecycle/ownership expectations.
- Field semantics (or equivalent) when non-obvious.
- Invariants and failure/edge behavior that maintainers must preserve.

For each function/method in scope (public and private):

- Purpose in the larger flow.
- Input assumptions and output semantics.
- Side effects (state changes, I/O, subprocesses, ordering).

For non-trivial functions/methods:

- Failure behavior and edge cases.
- Why the path exists when not obvious from signature/name.

Documentation quality floor:

- Do not restate names/signatures as standalone docs.
- Prefer contract/intent/constraints over implementation narration.
- Trivial code may have short docs, but they must still be informative.

## Core Workflow

1. Identify artifact(s): README, guide, API docs, design note, PR narrative, or in-code docs.
2. Clarify audience and goal (ask up to 3 total questions only when required).
3. Choose doc type via Diataxis (see `references/diataxis.md`).
4. Gather evidence from call sites, tests, CLI help output, and public API/error paths.
5. Draft with confidence tags when useful:
   - `Confirmed`: proven by code/tests/usages
   - `Inferred`: likely from context, not directly proven
   - `Unknown`: requires maintainer confirmation
6. Drift pass: verify names, flags, defaults, versions, invariants, and examples.
7. Review with `references/doc-review-rubric.md` and apply fixes.
8. Run a depth score pass (`L0` to `L3`) using `references/doc-depth.md`.
9. Run Unknown confirmation step before finalizing output.

## Required Resource Loading

Load these references before drafting, by mode:

1. `Greenfield Full Coverage Approach`:
   - Must load `references/doc-depth.md`.
   - Must load `references/docs-bootstrap.md`.
   - Must load `references/rustdoc.md`.
   - Must load `references/docs-remediation.md`.
   - Must load `references/doc-review-rubric.md`.
1. `Docstring / Rustdoc Mode`:
   - Must load `references/doc-depth.md`.
   - Must load `references/rustdoc.md`.
   - Must load `references/doc-review-rubric.md`.

If a required reference cannot be loaded, state this explicitly and continue with a fallback
summary of the missing guidance.

## Mode Output Contract

Always produce concrete outputs for the selected mode:

1. `Ratchet Mode`:
   - Updated docs patch (or exact edits) for touched scope.
   - Drift fixes list.
   - Follow-up items (numbered).
1. `Bootstrap Mode`:
   - Initial README/guide draft with runnable quick path.
   - Explicit assumptions and unresolved unknowns.
   - Next doc additions (numbered).
1. `PR Narration Mode`:
   - PR narrative text.
   - Contracts/invariants summary.
   - Required doc deltas and risks (numbered).
1. `Recovery Mode`:
   - Prioritized documentation backlog.
   - First remediation batch.
   - Remaining drift/unknown ledger.
1. `Bulk Update Mode`:
   - Batch summary and consistency changes.
   - Unresolved drift ledger.
   - Recommended next batch (numbered).
1. `Docstring / Rustdoc Mode`:
   - Updated docstrings/comments or exact edits.
   - Contract changes called out.
   - Test/doc alignment notes.
   - Coverage ledger (modules/types/functions documented vs total in scope).
   - Depth score summary (`L0`/`L1`/`L2`/`L3`) and upgrades for any `L0` items.
1. `Process Improvement Mode`:
   - Proposed standards text.
   - AGENTS.md update draft.
   - Adoption checklist (numbered).

## Repeatable Approaches

### Greenfield Full Coverage Approach

Use this when a new repository needs high documentation coverage, including private methods.

When this approach is selected from Goal Picker, run the full sequence by default unless the user
asks to limit scope.

Execution sequence:

1. Load required references listed in `Required Resource Loading`.
1. Run `Bootstrap Mode` as `owner`, `repo-wide`, `full`.
1. Run `Docstring / Rustdoc Mode` as `owner`, `repo-wide`, `full`.
1. Run `Recovery Mode` as `owner`, `repo-wide`, `full`.
1. Apply the first remediation batch in the same pass when feasible.
1. Run Unknown confirmation before finalizing.

Method coverage policy:

- Modules: purpose, boundaries, and invariants documented.
- Types: role, semantics, and invariants documented.
- Public methods/functions: full contract docs (purpose, inputs, outputs, failures, constraints).
- Private methods/functions: purpose, assumptions, invariants, and side effects documented.
- Any non-trivial method/function: edge cases and failure behavior documented.

Coverage gates:

- `touched-files` scope: target 100% non-test module/type/function doc coverage.
- `repo-wide` scope: target at least 95% non-test module/type/function doc coverage.
- If below target, continue doc pass iterations before finalizing.

Required final report:

1. Coverage summary for modules, types, and functions (public and private).
1. Remaining undocumented items (numbered, with file paths).
1. Highest-value next fixes (numbered).
1. Depth check summary (shallow docs and required upgrades).
1. At least 3 concrete shallow-to-improved rewrites when depth issues are found.

## Blocked/Fallback Behavior

If required context is missing:

1. Ask only the minimum blocking questions.
1. Proceed with explicit assumptions if unanswered.
1. Mark assumptions as `Unknown` and run final confirmation.

## Modes

### Ratchet Mode

Use when docs already exist but need quality improvements.

- Preserve existing tone and terminology.
- Improve clarity, correctness, and navigability.
- Remove contradictions and stale details.

### Bootstrap Mode

Use when a project has little or no documentation.

- Produce minimum viable docs: README + quick start + usage basics.
- Include assumptions/prerequisites and expected success output.
- Mark unknowns explicitly.
- See `references/docs-bootstrap.md`.

### PR Narration Mode

Use during PR creation/review.

- Write PR-level narrative (problem, model, tradeoffs, architecture impact).
- Extract contracts/invariants introduced or relied upon.
- List required documentation deltas and top risks.
- See `references/pr-guidelines.md` and `references/pr-review-guidelines.md`.

### Recovery Mode

Use for mixed-quality docs in existing codebases.

- Audit current docs for drift and coverage gaps.
- Prioritize fixes by impact and effort.
- Ship small, reviewable doc patches.
- See `references/docs-remediation.md`.

### Bulk Update Mode

Use for larger documentation sweeps.

- Apply shared glossary and style consistency.
- Minimize duplication and conflicting definitions.
- End with unresolved drift/unknowns report.
- See `references/bulk-doc-changes.md` and `references/tone-and-style-lock.md`.

### Docstring / Rustdoc Mode

Use when touching APIs or complex internals.

- Prioritize public APIs and high-complexity functions.
- Keep contracts/invariants explicit and test-aligned.
- Follow Rustdoc conventions from `references/rustdoc.md`.

### Process Improvement Mode

Use when the user is designing documentation standards for a repository/team.

- Suggest reusable repo-level standards, not one-off fixes.
- Include markdownlint configuration recommendations (100-char lines unless overridden).
- Recommend documentation workflows that match the target repo's existing conventions.
- If a repo uses a roadmap/planning artifact, update it in the repo's preferred format.
- Draft `AGENTS.md` updates that include docs standards and a pointer to the skill source.

## Quick Invocations

Use short one-liners instead of larger starter blocks:

```text
Use $doc-steward in Bootstrap mode as a contributor, focused on touched files, with a quick pass.
```

```text
Use $doc-steward in PR Narration mode as a contributor, focused on touched files, with a quick pass.
```

```text
Use $doc-steward in Recovery mode as a repo owner, with repo-wide scope and full detail.
```

```text
Use $doc-steward in Bulk Update mode as a repo owner, with repo-wide scope and full detail.
```

```text
Use $doc-steward in Process Improvement mode as a repo owner, repo-wide, with a quick pass.
```

```text
Use $doc-steward with the Greenfield Full Coverage approach as a repo owner, repo-wide, with full detail.
```

```text
Use $doc-steward in Docstring / Rustdoc mode as a repo owner, repo-wide, full detail, deep docs.
```

## Resources

Load only what is needed for the selected mode.

- `references/diataxis.md`: choose the right doc type and structure.
- `references/doc-review-rubric.md`: review checklist for quality and drift.
- `references/pr-guidelines.md`: PR authoring narrative templates.
- `references/pr-review-guidelines.md`: PR review checks and output format.
- `references/docs-bootstrap.md`: greenfield documentation bootstrap workflow.
- `references/docs-remediation.md`: mixed-quality documentation remediation workflow.
- `references/bulk-doc-changes.md`: controls and outputs for large doc passes.
- `references/tone-and-style-lock.md`: preserve local voice while improving clarity.
- `references/markdown-style.md`: markdown consistency and lintability.
- `references/comment-steward-prompts.md`: prompts/templates for comments/docstrings.
- `references/rustdoc.md`: Rustdoc patterns.
- `references/readme.md`: README section templates.
- `references/repo-docs-setup.md`: reusable repo standards and AGENTS.md guidance.
- `references/quick-modes.md`: short mode lines and profile dimensions.
- `references/doc-depth.md`: depth standards for modules, types, and functions.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/joshka) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
