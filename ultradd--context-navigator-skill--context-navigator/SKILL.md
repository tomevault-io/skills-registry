---
name: context-navigator
description: Bootstrap, navigate, maintain, and validate lightweight index systems for Markdown-heavy workspaces through hierarchical CONTEXT.md and INDEX.md files. Use when a user asks to make a docs folder, Obsidian vault, decision log, personal knowledge base, research folder, or project workspace AI-readable; asks where to start reading without recursively scanning every Markdown file; asks to create, review, repair, or validate workspace/domain indexes; or when existing indexes are missing, stale, or inconsistent. Do not use as the primary codebase navigation tool for ordinary TypeScript/Python/etc. source analysis, single-file edits, small bug fixes, or repositories whose useful entry points are package manifests, types, tests, and source structure rather than Markdown docs. When the user explicitly says to use this skill for a change that will create or edit files, first present a concise change plan and wait for confirmation before modifying files. Use when this capability is needed.
metadata:
  author: UltraDD
---

# Context Navigator

Context Navigator is an index system skill for Markdown-heavy workspaces. It can create a lightweight map for a messy folder, read an existing map without wasting context, validate index structure, and keep indexes current as the workspace grows.

The core promise: make folders AI-readable without turning every task into a recursive scan.

## First Gate: Explicit Use and File Changes

If the user explicitly says to use this skill and the request may create, update, move, or delete files, do not start editing immediately.

First provide a concise plan that states:

- the chosen mode: Navigate, Bootstrap, Maintain, or Validate
- the workspace scope that will be touched
- the likely files or directories to inspect
- the likely files to create or update
- the validation command or review check to run

Then wait for the user's confirmation before modifying files. If the user already confirmed the plan or says to proceed, continue. Pure read-only Navigate work does not need this extra confirmation.

## Workflow

1. Choose a mode: Navigate, Bootstrap, Maintain, or Validate.
2. Locate the smallest relevant workspace scope.
3. Prefer existing entry files before reading body documents.
4. Open or inspect only enough files to understand durable structure.
5. Create, update, or validate indexes only for important long-term folders.
6. Report what was planned or changed, what was read, what was skipped, and what remains a gap.

## Modes

| Mode | Use when | Primary action | Do not use when |
|---|---|---|---|
| Navigate | Indexes already exist and the user asks a question, asks where to start, or asks for decision/doc tracing | Read indexes, then 1-3 relevant body files | The user asked to create or repair indexes |
| Bootstrap | The user asks to organize a Markdown workspace, create indexes, make folders AI-readable, or onboard agents into a docs-heavy workspace with no durable entry map | Create a root map and key domain indexes | A repository only needs ordinary source-code analysis |
| Maintain | Existing indexes are stale because durable docs were added, moved, renamed, archived, or authority paths changed | Update the nearest relevant index instead of rebuilding everything | No durable docs or routes changed |
| Validate | The user asks whether indexes follow the expected structure, or before/after Bootstrap or Maintain | Run the bundled validator and fix schema/link issues when confirmed | The workspace has no intended Context Navigator index system |

## Trigger Thresholds

Use Bootstrap only when at least one of these is true:

- the user explicitly asks to bootstrap, organize, index, or make a Markdown workspace AI-readable
- the workspace has no durable root entry and has several long-term Markdown areas
- a folder mixes current authority, decisions, notes, reports, or archive material and future agents would not know where to start
- agents have already needed manual file listings to understand the same area

Use Maintain only when an existing index system is present and at least one of these is true:

- a durable document was added, moved, renamed, deleted, or archived
- an authority source or preferred entry file changed
- an index links to a missing path
- a child folder grew enough to need its own index
- the index schema or required sections fail validation

Do not trigger for vague phrases like "this repo is messy" unless the user also asks for docs/index organization or the workspace is clearly Markdown-heavy.

## Request Types

| Scenario | Action |
|---|---|
| User names one exact file | Read that file directly |
| User asks about a topic, directory, project, or knowledge area | Start from the nearest workspace or domain index |
| User asks why something was decided | Start from the decision index, then read the linked decision record |
| User asks to bootstrap or organize indexes | Read `references/bootstrap-workflow.md`, then create the smallest useful index system |
| User asks to maintain stale indexes | Read `references/maintenance-rules.md`, then update only the nearest affected indexes |
| User asks to validate indexes | Run `scripts/validate_indexes.py` from the skill folder |
| No index exists during ordinary navigation | List only the current directory one level deep, choose the most relevant 1-3 files, and report the index gap |
| User explicitly asks for a full audit | State that this bypasses progressive disclosure, then follow the user's instruction |

## Non-Markdown and Code Repository Boundary

For code-heavy repositories, this skill is a docs navigation layer, not a replacement for source analysis.

Prefer native code entry points for implementation work:

- `README.md`, package manifests, build config, and test config
- source entry files, route definitions, public APIs, and type definitions
- existing architecture docs or `docs/INDEX.md` only when they exist and are relevant

Do not generate indexes for every source directory. Create or maintain a docs index only when the repository has durable Markdown documentation, architecture notes, decisions, specs, runbooks, or project docs that future agents need to route through.

## Index Priority

Prefer the smallest index that can route the task.

| Scope | Preferred entry |
|---|---|
| Whole workspace | `CONTEXT.md`, `README.md`, `AGENTS.md`, or a root `INDEX.md` |
| Project or app | Project `README.md`, then `docs/INDEX.md` if present |
| Domain knowledge | Domain `INDEX.md` |
| Decisions | `decisions/INDEX.md` or equivalent decision log |
| Skill or procedural docs | The relevant `SKILL.md`, then only linked references |

If several entry points exist, pick the one that most directly matches the user's wording. Do not widen the search just because more documentation exists.

## Reading Protocol

1. Read the entry index and extract candidate paths, authority sources, and routing rules.
2. Select 1-3 candidate body files that directly answer the task.
3. Read those files in full.
4. If the answer is still under-supported, continue one level along the indexes.
5. Stop once the user-facing answer has enough evidence.

## Bootstrap Protocol

When asked to create an index system, read `references/bootstrap-workflow.md` and `references/index-template.md`.

Default outputs:

- root `CONTEXT.md` for a workspace-level map, unless a root `README.md` or `INDEX.md` is clearly the better entry point
- `INDEX.md` for durable domains, docs folders, decision logs, or recurring note/report folders
- a final report listing created indexes, skipped folders, and remaining gaps

Do not index everything. Skip dependency folders, build outputs, generated exports, hidden internals, caches, temporary folders, and archives unless the user explicitly includes them.

## Maintain Protocol

When asked to maintain indexes, read `references/maintenance-rules.md`.

Default behavior:

- identify the nearest existing `CONTEXT.md`, `INDEX.md`, or established `README.md`
- inspect only affected paths and their immediate parent index
- update routing, authority paths, gaps, and maintenance notes for durable doc changes
- remove or mark stale links when files moved or were archived
- run `scripts/validate_indexes.py <workspace-root>` when the workspace uses the schema

Never rebuild all indexes just because one route is stale.

## Validation Protocol

When asked to validate or after Bootstrap/Maintain, run:

```bash
python scripts/validate_indexes.py <workspace-root>
```

The validator checks Context Navigator index files for required frontmatter, required sections, and broken local links. Treat failures as actionable schema issues. If fixing them would modify files and the user has not confirmed a change plan, present the plan first.

## Report Format

When this skill is used for discovery, tracing, or synthesis, include a compact source note:

```markdown
Read indexes: `CONTEXT.md`, `docs/INDEX.md`
Read full files: `docs/architecture.md`
Not expanded: `archive/`, unrelated app docs
Gaps: `docs/decisions/` has no `INDEX.md`
```

For tiny tasks, use a sentence instead of a block.

## Guardrails

- Do not recursively read all Markdown files as the first move.
- Do not treat a file listing as an index unless no index exists.
- Do not build embeddings, a vector store, a database, or a custom search tool for the first pass.
- Do not open every related document merely because it is nearby.
- Do not cite "the docs" vaguely; name the indexes and full files you actually read.
- Do not convert temporary search results into authority. Authority comes from indexes or documents those indexes point to.

## Creating Indexes

When asked to create or review indexes, read `references/index-template.md`. A durable index should explain:

- what the scope covers
- what is outside the scope
- where authority sources live
- how common tasks should route
- what not to read first
- how the index should be maintained

New or substantially rewritten indexes should use the `context-navigator/v1` frontmatter schema from `references/index-template.md`. Existing legacy indexes can be maintained without bulk migration unless validation or user request requires schema adoption.

## Reference Routing

| Need | Read |
|---|---|
| Bootstrap a new index system | `references/bootstrap-workflow.md` and `references/index-template.md` |
| Create or review an index | `references/index-template.md` |
| Validate reading behavior during a task | `references/navigation-checklist.md` |
| Keep an indexed workspace healthy over time | `references/maintenance-rules.md` |
| Validate index schema and local links | Run `scripts/validate_indexes.py` |

## Adjacent Patterns

- RAG and embeddings retrieve chunks; Context Navigator decides what source path the agent should follow.
- Codebase scanners generate maps; Context Navigator teaches the agent how to use and maintain maps.
- Obsidian links connect notes; Context Navigator defines what an AI should read first.
- `AGENTS.md` gives agent instructions; Context Navigator gives documentation navigation discipline.
- Subagents may help with large read-only audits, but this portable skill does not hide any required subagent path. If a local installation adds agents, document when to call them in the local wrapper or project instructions.

---
> Source: [UltraDD/context-navigator-skill](https://github.com/UltraDD/context-navigator-skill) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-15 -->
