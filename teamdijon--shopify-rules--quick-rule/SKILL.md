---
name: quick-rule
description: Quickly draft a Shopify Liquid rule from official documentation and well-known patterns. Use when the topic is a well-documented Shopify feature, standard Liquid pattern, or common e-commerce technique that does not require deep cross-theme research. Faster alternative to research-shopify-rule. Use when this capability is needed.
metadata:
  author: teamdijon
---

# Quick rule -- write a rule from known patterns

You are writing a Shopify Liquid rule for a well-documented topic. This skill is the fast path: lean on Shopify's official documentation and your own knowledge rather than mining local themes.

The topic is: **$ARGUMENTS**

Read the reference files in this skill's directory before starting:

- [references/rule-template.md](references/rule-template.md) -- the format every rule must follow
- [references/existing-coverage.md](references/existing-coverage.md) -- what Shopify's cursor rules already cover (avoid duplicating)

---

## When to use this skill vs /research-shopify-rule

| Use `/quick-rule` when | Use `/research-shopify-rule` when |
|:---|:---|
| The pattern is well-documented on shopify.dev | The pattern has little or no official documentation |
| Shopify Liquid docs fully describe the objects and filters involved | The topic involves undocumented workarounds or community-discovered tricks |
| The topic is a single, focused technique | The topic spans multiple interacting patterns |
| Examples from official docs are sufficient | You need cross-theme comparison to find the best approach |
| Complexity is basic or intermediate | Complexity is advanced (nested loops, recursive-like patterns) |

If you discover during Phase 1 that the topic is more complex than expected, **stop and suggest switching to `/research-shopify-rule`** before investing time in a draft.

---

## Phase 1 -- Scope

### 1.1 Parse the topic

Break down `$ARGUMENTS`:

- What Liquid objects, filters, tags, or Shopify features are involved?
- Is this a single technique or does it combine multiple concepts?
- What complexity level does this feel like: basic, intermediate, or advanced?

### 1.2 Check for overlap

Use the three-level overlap check:

1. Read `references/existing-coverage.md` -- does this overlap with Horizon's cursor rules? If so, note what the existing rules cover and focus on what they do NOT cover.
2. Check `rules/INDEX.md` for existing rules on the topic. If a related rule exists, determine whether to extend it or create a new one.
3. If `shopify-themes/horizon/` is present, scan its `.cursor/rules/` for the latest coverage. This takes priority over the static summary.

Also check `rules/BACKLOG.md` -- if this topic is there, note its scope and evidence.

### 1.3 Complexity guardrail

Evaluate whether this topic is suitable for the quick path. Flag it as **too complex** if any of these are true:

- The official Shopify docs do not adequately document the pattern
- The topic involves undocumented Liquid behavior or workarounds
- Multiple interacting patterns are needed (more than one rule's worth of content)
- Real-world implementations vary significantly across themes and there is no clear best practice
- The topic requires understanding Shopify platform quirks not covered in docs

If the topic is too complex, tell the user:

> This topic appears more complex than a quick rule can cover well. Specifically: [reason]. I recommend using `/research-shopify-rule $ARGUMENTS` instead, which includes cross-theme research and pattern analysis phases.

Wait for the user to decide before proceeding.

### 1.4 Quick local scan (optional)

If the `shopify-themes/` directory contains themes, do a fast scan for relevant snippets or sections -- but only to supplement web research, not as a primary source. Limit this to a few targeted greps.

If `shopify-themes/` is empty or does not exist, skip this step entirely. The quick-rule workflow does not depend on local themes.

### 1.5 Present scope

Tell the user:

- The topic you understood from their input
- The target rule filename and category
- Any overlap with existing rules or cursor rules
- Whether you plan to create a new rule or extend an existing one
- Confirmation that the topic is suitable for the quick path

Wait for user confirmation before proceeding to Phase 2.

---

## Phase 2 -- Draft

### 2.1 Web research

Research the topic using Shopify's official documentation. Focus on:

- **shopify.dev** -- Liquid reference, theme architecture docs, API docs
- **Shopify Community forums** at community.shopify.dev -- for common gotchas and edge cases
- **Links the user provides** as additional context

For each key aspect of the pattern, look for:

- Official syntax and parameters
- Documented limitations or caveats
- Official code examples
- Changelog entries (when was this feature added or changed?)

### 2.2 Write the rule

Draft the rule following the template in `references/rule-template.md`. Apply these principles:

**Frontmatter**

- Choose the most appropriate category
- Set complexity to `basic` or `intermediate` (if you need `advanced`, reconsider whether this should be a `/research-shopify-rule`)
- Include tags that an AI agent would search for when encountering the problem
- List all Liquid objects and filters the pattern uses

**Problem section**

- Be specific about the gap -- what would an AI agent get wrong without this rule?
- Frame it as a limitation, challenge, or non-obvious behavior

**Solution section**

- Provide a complete, copy-paste ready code example
- Annotate each step so an AI agent understands the why, not just the what
- Use `{% liquid %}` blocks for multiline logic (Shopify convention)

**How it works section**

- Walk through each step of the pattern
- Explain why each line exists

**Variations section**

- Include alternative approaches if they exist in official documentation
- Note trade-offs for each variation
- If local themes were available and showed an interesting variation, include it with a source reference

**Source references section**

- Link to the relevant Shopify documentation pages (use full URLs)
- If themes in `shopify-themes/` were consulted, include theme file references
- If no local themes were available, state: "Pattern documented from Shopify official documentation."

**Edge cases and gotchas**

- Pull from documentation warnings, community forum issues, and known Liquid quirks
- Focus on things an AI would not know from reading the basic docs

**Related section**

- Link to related rules if they exist in `rules/`
- Suggest topics that could become future rules

### 2.3 Present draft

Show the complete draft to the user. Ask for feedback. Iterate based on their input.

---

## Phase 3 -- Finalize

1. Write the rule to `rules/<category>/<rule-name>.md`
   - Use kebab-case for filenames
   - Create the category folder if it doesn't exist yet
2. Update `rules/INDEX.md`:
   - Add the new rule under the appropriate category heading
   - Format: `- [Rule Title](category/rule-name.md) -- one-line description`
3. If this topic came from `rules/BACKLOG.md`, update the entry:
   - Set status to `done`
   - Add the rule link
4. Suggest 1-2 related topics that could become future rules

---

## Important guidelines

- Never invent Liquid filters, tags, or objects. Only use what exists in Shopify's Liquid implementation.
- When showing code examples, use the `{% liquid %}` tag for multiline logic blocks.
- Always test that code examples are syntactically valid Liquid.
- If a pattern involves metafields, specify the metafield namespace and type clearly.
- Cross-reference Shopify's official Liquid docs when unsure about object properties or filter behavior.
- The rules are standalone content. Do not reference project-specific paths within rule content.
- Source references should use full URLs for Shopify docs and relative theme paths for local themes (e.g., `dawn/snippets/price.liquid`).
- This is the fast path. If research is taking significantly longer than expected, reconsider whether `/research-shopify-rule` would be more appropriate.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/teamdijon) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
