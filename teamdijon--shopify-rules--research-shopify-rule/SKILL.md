---
name: research-shopify-rule
description: Research Shopify Liquid patterns across themes and produce AI-consumable rule documents. Use when creating new Shopify development rules, documenting Liquid patterns, researching theme techniques, or when the user mentions writing a rule about a Shopify topic. Use when this capability is needed.
metadata:
  author: teamdijon
---

# Research and create a Shopify theme development rule

You are researching Shopify Liquid patterns to produce a well-documented rule for AI consumption. The topic is: **$ARGUMENTS**

Read the reference files in this skill's directory before starting:

- [references/existing-coverage.md](references/existing-coverage.md) -- what Shopify's cursor rules already cover (avoid duplicating)
- [references/theme-guide.md](references/theme-guide.md) -- recommended themes and how to search them
- [references/rule-template.md](references/rule-template.md) -- the format every rule must follow

---

## Phase 1 -- Scope

1. Identify the core topic from `$ARGUMENTS`. Break it down: what Liquid objects, filters, tags, or Shopify features are involved?
2. Check for overlap using the three-level approach:
   - Read `references/existing-coverage.md` for a summary of what Horizon's cursor rules cover. If the topic overlaps, note what the existing rules cover and focus on what they do NOT cover (advanced patterns, workarounds, edge cases).
   - Check `rules/INDEX.md` for rules already written in this repo.
   - If `shopify-themes/horizon/` is present, scan its `.cursor/rules/` directory for the latest coverage. This takes priority over the static summary in `existing-coverage.md`.
3. Search `rules/` for existing rules on the topic. If a related rule exists, determine whether to extend it or create a new one.
4. Present your scope assessment to the user before proceeding.

## Phase 2 -- Research

Search for patterns across the themes in `shopify-themes/`. Use Explore agents to parallelize the search.

If `shopify-themes/` is empty or has few themes, expand web research. The skill can still produce valuable rules from Shopify documentation alone, though cross-theme comparisons will be limited. Suggest the user adds themes for richer research -- see `references/theme-guide.md` for recommendations.

### What to search for

- **Snippet files** related to the topic (search by filename and content in `snippets/` folders)
- **Section files** that implement related functionality (in `sections/` folders)
- **Liquid patterns** using relevant filters, tags, and objects
- **Comments** mentioning workarounds, tricks, or explanations (search for terms like "workaround", "hack", "trick", "note:", "important:", "TODO")
- **Schema patterns** in section `{% schema %}` blocks that relate to the topic

### Where to search

Search all themes in `shopify-themes/`. See `references/theme-guide.md` for details on recommended themes and search strategies.

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

### Research output

For each pattern found, record:

- **Source**: theme name and file path
- **Code**: the relevant Liquid snippet
- **Context**: what problem it solves, how it's used in the theme
- **Quality**: is it clean, hacky, well-commented, production-tested?

## Phase 3 -- Analysis

1. **Catalog** all discovered patterns with source references
2. **Compare** approaches across themes -- what's common, what varies, what's unique?
3. **Rank** patterns by:
   - Correctness (does it handle edge cases?)
   - Elegance (is it readable and maintainable?)
   - AI-reproducibility (can an AI agent reliably generate this from a description?)
4. **Identify** the recommended pattern(s) and note why alternatives are weaker
5. **Document** edge cases, gotchas, and Liquid quirks relevant to this topic

Present a structured summary of findings to the user. Include code snippets from the most interesting patterns.

## Phase 4 -- Draft

Discuss with the user:

- Should this be one rule or multiple rules?
- Should it extend an existing rule?
- What category does it belong in?

Then draft the rule following the template in `references/rule-template.md`. Key principles:

- **Write for AI agents**: the rule should help an AI turn a high-level requirement into correct Liquid code. Explain the _why_ behind each line, not just the _what_.
- **Include complete, working examples**: don't just describe patterns, show them with full context.
- **Document the non-obvious**: focus on things an AI wouldn't know from Liquid docs alone -- workarounds, quirks, community conventions.
- **Be precise about Liquid limitations**: if a pattern exists because Liquid can't do something natively, say so explicitly.
- **Reference source themes**: include where the pattern was found so the user can verify.

Present the draft to the user for review. Iterate based on feedback.

## Phase 5 -- Finalize

1. Write the rule file(s) to `rules/<category>/<rule-name>.md`
   - Use kebab-case for filenames
   - Choose the most appropriate category from the recommended categories (see README or `references/rule-template.md`)
   - Create the category folder if it doesn't exist yet
2. Update `rules/INDEX.md`:
   - Add the new rule under the appropriate category heading in the Rules section
   - Format: `- [Rule Title](category/rule-name.md) -- one-line description`
3. If this topic came from `rules/BACKLOG.md`, update the corresponding entry's status to `done` and add the rule link
4. Suggest related topics that could become future rules based on what you discovered during research

---

## Important guidelines

- Never invent Liquid filters, tags, or objects. Only use what exists in Shopify's Liquid implementation.
- When showing code examples, use the `{% liquid %}` tag for multiline logic blocks (Shopify convention for newer themes).
- Always test that code examples are syntactically valid Liquid.
- If a pattern involves metafields, specify the metafield namespace and type clearly.
- Cross-reference Shopify's official Liquid docs when unsure about object properties or filter behavior.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/teamdijon) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
