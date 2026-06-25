---
name: writing-tech-specs
description: Use when writing or revising an HTML-first technical design package, technical plan, implementation plan, workflow plan, process flow plan, migration plan, rollout plan, cutover plan, RFC, architecture spec, interface contract, schema, state machine, or implementation spec for engineers or the next coding agent.
metadata:
  author: yin52133
---

# Writing Technical Specs And Plans

## Overview

Write specs and plans that let an implementer start work without guessing.

This skill is guidance, not a repository-local file policy. Follow the current project's canonical docs, filenames, and section layout when they already work. Introduce new structure only when the existing structure is missing, misleading, or explicitly in scope to change.

Generalized does not mean structure-free. When a project does not already have a good canonical shape, this skill still expects a standard package, standard section discipline, and explicit review gates so the spec normalizes the request into a buildable design.

Default new design output is HTML. Markdown is a legacy fallback for existing Markdown-only canonical docs, explicit user requests, or review systems that cannot render HTML. Execution checklists remain JSON.

Plans must be goal-driven. Each implementation slice needs an explicit goal, the smallest necessary change boundary, and a verification signal that can pass or fail.

## Truthfulness and Failure Semantics

Be factual. Do not add user-pleasing filler or write a smoother design than the real constraints support.

If a requested requirement is inconsistent, unsafe, underspecified, or technically wrong:
- say so directly
- mark the broken assumption, boundary, or risk
- ask for the missing decision or change the design explicitly
- do not paper over the problem with vague compatibility language

Do not add fallback or error handling by reflex.
If invalid input, a missing dependency, a broken precondition, or a contract violation should stop execution, the spec should say so and define the error outcome.
Only add retry, degradation, skip, best-effort, or partial-success behavior when that behavior is an actual requirement. When such behavior exists, define the trigger, visibility, preserved invariants, and operator-facing outcome explicitly.

## Hard Language Rule

In newly written or substantively edited text, do not use contrastive slogans such as `不是X，而是Y` or `not X, but Y`.

Bad:
- `重点不是兼容性，而是确定性。`
- `This plan is not about speed, but reliability.`

Good:
- `重点是确定性；兼容性按现有接口处理。`
- `This plan optimizes for reliability. Performance work is out of scope for this revision.`

State the decision directly. Do not manufacture rhetorical reversal for emphasis.
Do not widen an incremental revision just to rewrite untouched canonical text for this rule alone.

## When to Use

Use this skill when the user wants to:

- write an implementation plan
- write an HTML design package with an `index.html` entry point and submodule pages
- write a workflow plan or process flow plan for a technical change
- write a migration, rollout, or cutover plan
- write or revise an architecture doc or RFC
- define interfaces, schemas, state machines, or storage rules
- turn requirements into a buildable implementation spec
- update an existing technical spec without rewriting it blindly
- apply a spec change that creates or changes pending implementation work and propagate that delta to the execution tracker (checklist, task list, or equivalent)

Do not use this for:

- narrative status updates
- meeting notes preserved as-is
- generic project-management checklists with no technical design content
- user-facing marketing or launch copy

## Core Rule

A good spec or plan answers these five questions clearly:

1. What problem is being solved?
2. What are the system boundaries?
3. What happens in what order?
4. What data, interfaces, or operational rules are required?
5. How does an implementer know they are done?

If the document cannot answer all five, it is not ready.

## Anti-Repetition Rule

Every paragraph, bullet, table, and phase entry must add net-new information.

Do not:
- restate the same decision in prose, bullets, and phase lists
- paraphrase the user's request just to make the document longer
- repeat motivation or scope language across multiple sections
- restate the same flow in both prose and diagram unless each adds different information
- use implementation phases to re-summarize the system instead of describing distinct work slices

Deletion test:
- if a sentence can be removed without losing a decision, dependency, interface, owner, risk, or done signal, delete it
- if two adjacent bullets say the same thing with different wording, merge them

## Incremental Update Rule

When updating an existing design, default to incremental modification, not parallel rewrite.

Incremental update means:
- keep the existing canonical doc set, file paths, and section structure when they are still correct
- preserve goals, boundaries, flows, and contracts that still match reality
- change only the parts that are actually wrong, stale, or incomplete
- update downstream references in the same pass when a changed term, contract, or flow appears elsewhere

Precedence rule for legacy canonical specs:
- if the existing canonical spec set predates this skill's default package or section layout, treat the legacy layout as authoritative for this update
- do not force an old canonical spec set into the default `index.html` package or section order just to match this skill
- migrate to the default package or section layout only when the current file/package structure is itself wrong, misleading, or explicitly in scope for the revision

Incremental update is not limited to renaming. It may include:
- changing responsibilities between layers or components
- changing data structures or schema contracts
- changing flow order or runtime behavior
- replacing outdated design rules with new ones

But it must stay scoped:
- modify the affected design area, not the whole system by reflex
- do not invent a parallel terminology set unless the old one is being explicitly replaced in the canonical docs
- do not paste conversation terms directly into the spec without reconciling them against the original design

If current runtime behavior still follows the old path while the spec describes the target behavior, mark that boundary explicitly. Do not present target-state contracts as if they are already runnable.

When a changed design point has downstream references, audit and update them together:
- README links and architecture entry points
- integration docs
- examples and command snippets
- schema names, file names, and object names
- neighboring spec sections that still describe the old behavior

## Output Guidance

Prefer the smallest clear document set:
- one canonical entry document for the change, defaulting to `index.html` for new design packages
- optional companion files only when they carry distinct content
- at most one canonical execution tracker, as `checklist.json`, if the repo uses one for the same workstream or tracking is part of the requested output
- at most one revision log, if the repo keeps one

If no canonical package exists, use the default package and section discipline from `rules/output-package.md` and `rules/required-sections.md`.
Do not force a filename pattern when the project already has a good one.

## Rules

Detailed rules are in the `rules/` directory:

- [Output package guidance](rules/output-package.md) — the default package shape, split triggers, and how to adapt it without forcing local conventions
- [HTML output rules](rules/html-output-rules.md) — HTML-first package rules, Mermaid rendering setup, and JSON checklist format
- [Default section set](rules/required-sections.md) — the default section order, conditional sections, and what a buildable main spec usually needs
- [Diagram rules](rules/diagram-rules.md) — when to draw diagrams, when not to, and how HTML-first output changes the default
- [Mermaid UML rules](rules/mermaid-uml-rules.md) — Mermaid UML diagram types and scenario-based selection guidance
- [Filesystem layout rules](rules/filesystem-layout-rules.md) — how to document repo trees, ownership boundaries, and run-scoped output layouts
- [Contract rules](rules/contract-rules.md) — interface, schema, state machine, and storage semantics format
- [Acceptance rules](rules/acceptance-rules.md) — how to write checkable completion criteria
- [Goal-driven development rules](rules/goal-driven-development-rules.md) — assumptions, simplicity boundaries, surgical changes, and phase verification loops
- [Style rules](rules/style-rules.md) — what to exclude, how to keep prose direct, and how to avoid rhetorical filler
- [Review checklist](rules/review-checklist.md) — hard gate before handoff

---
> Source: [yin52133/codex-write-specs](https://github.com/yin52133/codex-write-specs) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-23 -->
