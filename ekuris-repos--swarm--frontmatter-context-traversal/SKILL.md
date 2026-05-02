---
name: frontmatter-context-traversal
description: Use YAML frontmatter fields (title/description/tags/related/links_from) to traverse docs and pull the minimum relevant context into the session. Supports tag-based discovery for multi-concept queries and configurable traversal depth. Use when this capability is needed.
metadata:
  author: ekuris-repos
---

# Frontmatter-Driven Context Traversal

## Goal

Before implementing changes, bring in **only the minimum context** needed by using YAML frontmatter to locate and traverse the most relevant Markdown files.

This skill is designed for repos where docs are connected via frontmatter fields like `related` and `links_from`.

For consistency, represent `related` and `links_from` as YAML inline arrays where possible:

```yaml
related: ["knowledge-base/README.md", ".github/copilot-instructions.md"]
links_from: ["README.md"]
```

## Inputs

- The user’s current ask (feature/bug/refactor/docs change)
- Any explicitly mentioned files
- Frontmatter fields in Markdown docs- **Traversal depth** (optional, default: 1)
  - `depth: 0` — Entry document only, no expansion
  - `depth: 1` — Entry document + immediate `related`/`links_from` (default)
  - `depth: 2` — Two hops from entry point (use for cross-cutting concerns)
  - `depth: 3+` — Rarely needed; use explicit justification
## Output

A small set of high-signal files (typically 1–6) that you read/search to understand:

- The current contract / expected behavior
- The relevant constraints and adjacent components
- The right place to make the change

## Process (Minimum-Context First)

1. Extract 3–8 key terms from the ask.
   - Include product terms, component names, commands, and file types.
2. Find candidate docs quickly:
   - Prefer semantic search for concepts (titles, intent, usage).
   - Prefer text/regex search for exact tokens (file paths, command names).
   - **Use tag-based discovery** for intersection queries (see below).
3. Read the YAML frontmatter of the best 1–3 candidates.
4. Build a small traversal set (respecting configured depth):
   - **Depth 0**: Stop at entry document.
   - **Depth 1** (default): Include candidate's `related` and high-signal `links_from`.
   - **Depth 2+**: Expand one additional hop per depth level.
   - Stop expanding once you have enough context to act OR reach depth limit.
5. Search within the traversal set only:
   - Use semantic search to locate the relevant section.
   - Use exact search for identifiers (symbols, config keys, CLI flags).
6. Decide and proceed:
   - If context is sufficient, implement.
   - If context is insufficient and depth allows, expand by one hop and repeat.
   - If at depth limit and still insufficient, request user guidance or increase depth.

## Tag-Based Discovery

When a question spans multiple concepts, use tag intersection to find documents that bridge them:

### Process

1. Identify 2–3 core concepts from the ask (e.g., "async", "error-handling", "streaming").
2. Search for documents where `tags` contains multiple target concepts:
   ```
   grep: tags:.*async.*error|tags:.*error.*async
   ```
3. Alternatively, search for tag arrays containing specific values:
   ```
   grep: "async".*"error-handling"|"error-handling".*"async"
   ```
4. Documents matching multiple tags are high-value candidates for entry points.

### Example

**Question**: "How do I handle errors in async streaming code?"

**Tag search**: Find docs with tags intersecting `[async, error-handling, streaming]`

| Document | Tags | Match Score |
|----------|------|-------------|
| async-streams.md | async, streaming, linq | 2/3 |
| result-pattern-vs-exceptions.md | error-handling, async | 2/3 |
| exception-handling-best-practices.md | error-handling | 1/3 |

**Result**: Prioritize `async-streams.md` and `result-pattern-vs-exceptions.md` as entry points.

### When to Use Tag Discovery

- Question spans multiple domains (async + error handling + specific pattern)
- Direct keyword search returns too many results
- Looking for "bridge" documents that connect concepts
- `related`/`links_from` graph is sparse or incomplete

## Indexing Guidance

When you create or update Markdown docs, keep these frontmatter fields usable for traversal:

- `tags`: include common lookup terms and synonyms
- `related`: include strongly connected peers
- `links_from`: include canonical entry points that should reference this doc

When starting work, treat `related` and `links_from` as a lightweight, human-maintained graph.

## Heuristics

- Prefer files with:
  - matching tags or category
  - **multiple matching tags** (intersection = higher relevance)
  - a very specific title/description
  - high fan-in (`links_from` is non-empty)
- Prefer reading less:
  - pull the exact sections you need, not entire documents
- Avoid "context hoarding":
  - don't load a whole folder unless necessary
- **Depth selection heuristics**:
  - Simple lookup (definition, syntax): `depth: 0`
  - Standard question (how-to, pattern): `depth: 1`
  - Cross-cutting concern (spans multiple domains): `depth: 2`
  - Architecture/design decision: `depth: 2–3`

## Tooling Notes

If available in the environment:

- Use semantic search to find conceptually relevant docs and sections.
- Use exact search (regex/grep) to find frontmatter keys and precise identifiers.
- Read full files only when the relevant section can’t be isolated.

## Guardrails

- Don’t assume `related`/`links_from` are perfectly maintained; verify quickly.
- If frontmatter is missing, consider applying the `markdown-frontmatter` skill first.
- Keep the context set small; expand only when blocked.- **Respect depth limits**: If configured depth is exhausted, surface what's missing rather than silently expanding.
- **Document missed context**: When answering with limited depth, note what related topics were not explored.

## Depth Selection Guide

| Question Type | Recommended Depth | Example |
|---------------|-------------------|---------|
| Definition lookup | 0 | "What is ConfigureAwait?" |
| Single-concept how-to | 1 | "How do I use CancellationToken?" |
| Multi-concept pattern | 1–2 | "Error handling in async streams" |
| Cross-cutting design | 2 | "Resilience patterns with retry and cancellation" |
| Architecture decision | 2–3 | "Should I use Result pattern or exceptions throughout?" |

When in doubt, start with `depth: 1` and note any gaps in the response.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ekuris-repos) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
