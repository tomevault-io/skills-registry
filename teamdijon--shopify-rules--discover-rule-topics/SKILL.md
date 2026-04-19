---
name: discover-rule-topics
description: Discover potential rule topics by mining patterns across Shopify themes. Use when exploring what rules to write next, finding patterns in themes, or building a backlog of rule ideas. Complements research-shopify-rule and quick-rule by identifying topics rather than writing rules. Use when this capability is needed.
metadata:
  author: teamdijon
---

# Discover rule topics from Shopify themes

You are exploring Shopify themes to discover patterns, techniques, and conventions that could become AI-consumable rules. Your goal is to enrich the topic backlog, NOT to write rules.

The search focus is: **$ARGUMENTS**

If `$ARGUMENTS` is empty or very broad (e.g., "general", "explore"), perform a wide exploration across multiple categories. If it names a specific area (e.g., "CSS patterns", "cart AJAX API", "metafield traversal"), focus your search on that domain.

Read these files before starting:

- [references/category-definitions.md](references/category-definitions.md) -- extended category taxonomy with search hints
- [references/existing-coverage.md](references/existing-coverage.md) -- what Shopify's Horizon cursor rules already cover (avoid re-discovering these)
- `rules/BACKLOG.md` -- existing topic backlog (don't re-discover these)
- `rules/INDEX.md` -- completed rules (don't suggest already-covered topics)

---

## Phase 1 -- Orient

1. Parse `$ARGUMENTS` to determine the search scope:
   - **Focused**: a specific technology area, pattern type, or Shopify feature
   - **Broad**: no arguments, or a high-level term like "explore" or "general"
2. Read `rules/BACKLOG.md` to load existing topics.
3. Read `rules/INDEX.md` to load completed rules.
4. Check for Horizon cursor rule coverage using the three-level approach:
   - If `shopify-themes/horizon/` is present, list files in `shopify-themes/horizon/.cursor/rules/` for the latest coverage.
   - If Horizon is not present, consult `references/existing-coverage.md` for a summary of what Shopify's cursor rules cover.
   - Either way, also check `references/existing-coverage.md` for the "What is NOT covered" section -- these are prime opportunities for new rules.
5. Build an exclusion set from all sources -- these are topics you must NOT re-discover.

## Phase 2 -- Search

Search across all themes in `shopify-themes/`. Use Explore agents to parallelize searches.

If `shopify-themes/` is empty or contains no themes, lean on web research instead. Suggest the user adds themes to `shopify-themes/` for richer discovery -- see the project README for recommended themes (Dawn and Horizon as starting points).

### Search strategy by scope

**If focused on a specific area:**

- Use `references/category-definitions.md` to find the relevant search patterns
- Deep-dive: search filenames, file contents, and comments across all themes
- Compare how different themes solve the same problem
- Note interesting variations and workarounds

**If broad (no specific focus):**

- Sample across multiple categories using the search hints in `references/category-definitions.md`
- Prioritize areas with the most cross-theme variation
- Look for patterns that combine multiple technologies (Liquid + CSS, Liquid + JS, etc.)
- Start with `snippets/` and `assets/` directories for the richest pattern density

### General search techniques

1. **Filename patterns**: glob for files with meaningful names across themes
   - `shopify-themes/*/snippets/*<keyword>*`
   - `shopify-themes/*/assets/*<keyword>*.{css,js}`
2. **Content patterns**: grep for specific terms from `references/category-definitions.md`
3. **Cross-theme comparison**: find the same snippet or pattern name in multiple themes and note how implementations differ
4. **Comment mining**: search for `workaround`, `hack`, `trick`, `TODO`, `FIXME`, `NOTE:`, `IMPORTANT:`

### Web research (when appropriate)

By default, focus on local theme research when themes are available. Expand to web research when:

- The user explicitly asks for it or shares links
- The user's topic involves a Shopify feature where official docs would add clarity
- Local themes don't have sufficient examples
- `shopify-themes/` is empty

Recommended web sources:

- Shopify Dev Docs -- use the Shopify Dev MCP tool if available, otherwise `shopify.dev`
- Shopify Community forums at `community.shopify.dev`
- Links the user provides as additional context

## Phase 3 -- Evaluate

For each candidate topic found during search:

1. **Check against exclusion set**: Is it already in the backlog? Already a rule? Covered by Horizon cursor rules?
2. **Assess generalizability**: Would this help someone building any Shopify theme, or just one specific project?
3. **Assess AI-value**: Would an AI agent get this wrong without explicit guidance? If the pattern is obvious from Shopify docs alone, skip it.
4. **Determine category**: Which category does this belong to? Use the 12 categories from `references/category-definitions.md`.
5. **Gauge complexity**: Is there enough substance for a full rule document? Too thin = not worth it.
6. **Count evidence**: How many themes exhibit this pattern? More themes = higher priority.

### Present candidates to the user

For each candidate topic, show:

- **Proposed title** (specific enough to be a `/research-shopify-rule` or `/quick-rule` invocation)
- **Category**
- **Why it matters** (1-2 sentences)
- **Evidence** (which themes, what file types)
- **Overlap risk** (anything close in existing coverage?)

The user approves which topics to add to the backlog.

## Phase 4 -- Enrich

After user approval, append the approved topics to `rules/BACKLOG.md`.

### Enrichment rules

1. **Append only**: never remove or modify existing topics (except to update status)
2. **No duplicates**: if a topic substantially overlaps with an existing backlog entry, skip it or suggest merging with the existing entry
3. **If nothing found**: if the search produced no viable new topics, say so honestly and leave the file unchanged. Do not add filler.
4. **Preserve structure**: follow the topic entry format exactly

### Topic entry format

Add each topic under the `## Topics` heading in `rules/BACKLOG.md`:

```markdown
### <Topic Title>

- **Category**: category-slug
- **Status**: `pending`
- **Scope**: 1-3 sentences describing what the rule would cover
- **Evidence**: which themes contain relevant patterns (theme names only)
- **Search terms**: keywords someone could grep for to find the relevant code
- **Added**: YYYY-MM-DD
- **Rule**: _pending_
```

After enriching the backlog, suggest what the user might explore next -- either a deeper dive into the same area, or a different category entirely.

---

## Important guidelines

- This skill discovers and queues topics. It does NOT write rules. Point users to `/research-shopify-rule` for complex topics or `/quick-rule` for well-documented patterns.
- Each topic should be scoped to roughly one rule document. If a topic is too broad, split it into multiple entries.
- Prefer specific, actionable titles over vague ones. "CSS custom properties for theme color schemes via Liquid settings" is better than "CSS variables".
- The scope of discoverable topics is deliberately broad: project config, HTML, CSS, JavaScript, Liquid, e-commerce patterns, Shopify specifics, and combinations thereof.
- Reference themes generically by name (e.g., "Dawn, Horizon, Vision") without full system paths.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/teamdijon) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
