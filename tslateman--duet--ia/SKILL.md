---
name: ia
description: Evaluate and improve how information is organized for findability. Use when the user asks to "organize this", "restructure docs", "improve navigation", "where should this go", "review file structure", struggles to place content, or when documentation grows beyond a single page. Use when this capability is needed.
metadata:
  author: tslateman
---

# Information Architecture

## Overview

Information architecture is the structural design of shared information environments. When someone asks "where does this go?" or "I can't find anything" — that's an IA problem.

Grounded in Rosenfeld, Morville & Arango's _Information Architecture_ (4th ed.) and the Diataxis documentation framework. See `references/rosenfeld-morville.md` for the core systems.

## When to Use This Skill

- Organizing a new project's file structure
- Restructuring documentation that outgrew its layout
- Deciding where new content belongs
- Reviewing a README or docs site for findability
- Designing navigation (tables of contents, cross-references, indexes)
- Auditing content for gaps, duplication, and orphans

**If someone can't find what they need, or you can't decide where something belongs, use this skill.**

## The Four Systems

Every information space has four structural systems. Evaluate each when reviewing or designing.

### 1. Organization

How content is grouped and classified.

**Exact schemes** — unambiguous, mutually exclusive:

- Alphabetical (glossaries, API references)
- Chronological (changelogs, history tables)
- Geographic (regional docs, locale configs)

**Ambiguous schemes** — subjective, require judgment:

- By topic (most documentation)
- By task ("How do I...")
- By audience (developer vs. operator vs. user)
- By metaphor (domain-driven structures)

**Rule:** Choose one primary scheme per level. Mixing schemes at the same level confuses navigation.

#### Taxonomy Construction

When reviewing a project's organization, evaluate whether the taxonomy is sound:

1. **Can every item be placed in exactly one group?** — If not, categories
   overlap. Tighten the scheme.
2. **Are items at the same level comparable in scope?** — "utils" alongside
   "authentication" mixes granularity.
3. **Does depth exceed three levels?** — Over-splitting signals the taxonomy
   is too fine-grained. Flatten by merging related categories.
4. **Can a newcomer predict where to find something?** — If not, the grouping
   reflects how the author thinks, not how readers seek.
5. **Can a newcomer predict where to put something new?** — If not, the scheme
   has gaps or ambiguous boundaries.

**Card sort heuristic:** If three people disagree on where an item belongs, the
categories are ambiguous. Rename or restructure until placement is obvious.

### 2. Labeling

What things are called — the names users scan to orient themselves.

- Labels must describe content, not container ("Authentication" not "Section 3")
- Consistent granularity — don't mix concepts and tasks at the same level
- Match user vocabulary — use the words people search for
- Avoid jargon at top levels; introduce it inside

### 3. Navigation

How people move through the space.

- **Global navigation** — always visible, orients the reader (README, sidebar, table of contents)
- **Local navigation** — within a section (prev/next, related pages, "See Also")
- **Contextual navigation** — inline links from content to related content
- **Supplemental navigation** — indexes, site maps, search

**Depth rule:** Three clicks (or three levels) to any content. If deeper, the hierarchy needs flattening or the content needs relocating.

### 4. Search

How people find specific things without browsing.

- File naming that greps well (`signal-contract.md` not `doc-7.md`)
- Headings that match search terms
- Cross-references from synonyms to canonical locations
- Consistent metadata (frontmatter, tags) for programmatic discovery

## Diataxis for Documentation

When the content is documentation, apply the Diataxis framework to classify pages:

| Mode        | Orientation   | Purpose                  | Form           |
| ----------- | ------------- | ------------------------ | -------------- |
| Tutorial    | Learning      | Teach through doing      | Lesson         |
| How-to      | Task          | Solve a specific problem | Recipe         |
| Explanation | Understanding | Clarify concepts         | Discussion     |
| Reference   | Information   | Describe the machinery   | Austere, exact |

**Each page serves one mode.** Mixing tutorial prose into a reference page degrades both. When a page feels unfocused, it likely conflates two modes.

## Review Workflow

### 1. Map the Current State

Inventory the information space:

- List all content (files, sections, pages)
- Identify the primary organization scheme at each level
- Note orphans (content with no navigation path to it)
- Note duplicates (same information in multiple places)
- Note gaps (questions users would ask that have no answer)

### 2. Evaluate the Four Systems

For each system, ask:

| System       | Question                                         |
| ------------ | ------------------------------------------------ |
| Organization | Is the grouping scheme consistent at each level? |
| Labeling     | Can a reader predict content from the label?     |
| Navigation   | Can someone reach any content in three steps?    |
| Search       | Do file names and headings match search terms?   |

### 3. Recommend Changes

Structure output as:

```markdown
## IA Review

### Structure Issues

- [Issue] — [Why it hurts findability] → [Recommended fix]

### Navigation Gaps

- [Missing path] — [Who needs it] → [Where to add it]

### Labeling Problems

- `[current label]` → `[better label]` — [Why]

### Content Gaps

- [Missing topic] — [Who needs it, when]
```

## Common Patterns

### The Growing README

READMEs that accumulate everything eventually fail. When a README exceeds ~200 lines, extract:

- Setup instructions → `docs/setup.md` (how-to)
- Architecture overview → `docs/architecture.md` (explanation)
- API reference → `docs/api.md` (reference)
- Tutorial walkthrough → `docs/tutorial.md` (tutorial)

The README becomes a signpost: project description, quick start, and links to the rest.

### The Flat Docs Directory

A `docs/` with 20+ files at one level signals missing hierarchy. Group by topic or audience, not by creation date.

### The Deep Nest

More than three directory levels for documentation means the taxonomy is too fine-grained. Flatten by merging related pages or promoting important content.

### The Orphan Page

Content exists but nothing links to it. It might as well not exist. Every page needs at least one navigation path leading to it.

## Placement Decision Tree

When deciding where new content goes:

1. **Does similar content already exist?** → Extend it, don't duplicate
2. **What question does it answer?** → Place it where someone asking that question would look
3. **Who needs it?** → Place it in the audience's natural path
4. **What Diataxis mode is it?** → Group it with the same mode
5. **Can you name the parent directory in one word?** → If not, the taxonomy needs work

## See Also

- `/naming` — IA labeling problems are naming problems
- `/design` — IA is structural design for information
- `/prose` — Clear writing makes content findable through scanning
- `skills/FRAMEWORKS.md` — Full framework index

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/tslateman) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
