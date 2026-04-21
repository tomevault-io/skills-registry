---
name: glean
description: Surface emergent patterns and insights from the AI-ready vault memory system. Use periodically to discover connections between memories, identify recurring themes, and generate meta-insights that aren't obvious from individual memories. Use when this capability is needed.
metadata:
  author: robabby
---

# Glean

Surface emergent patterns and insights from memory.

## Purpose

Individual memories capture discrete information. Gleaning reveals:
- Connections between seemingly unrelated memories
- Recurring themes or patterns
- Evolution of thinking over time
- Gaps or contradictions in stored knowledge
- Meta-insights that emerge from the collection

## Workflow

1. Load memories for analysis
   - If $ARGUMENTS provided, focus on that domain
   - Otherwise, analyze across all memory types
   - Prioritize recent and high-importance memories

2. Identify patterns
   - Recurring concepts across memories
   - Related decisions or approaches
   - Evolving understanding of topics
   - Clusters of related information

3. Surface insights
   - What themes emerge?
   - What connections weren't obvious?
   - What contradictions exist?
   - What gaps should be filled?

4. Optionally store meta-memories
   - Patterns discovered can become new memories
   - Type: Usually Strategic or Procedural

## Analysis Dimensions

| Dimension | Questions |
|-----------|-----------|
| **Temporal** | How has thinking evolved? What changed? |
| **Conceptual** | What themes recur? What clusters form? |
| **Contradictions** | Where do memories conflict? |
| **Gaps** | What's missing? What should be captured? |
| **Connections** | What links across domains? |

## Parameters

- `$ARGUMENTS` (optional): Domain or topic to focus analysis on

## Default Paths

Analyzes memories at: `Areas/AI/Memory/`

## Related Skills

- `/recall {terms}` - Search for specific memories
- `/remember` - Store patterns discovered as new memories
- `/reflect` - Session-end memory consolidation

## Output Format

**Patterns Observed**:
- List of 3-5 patterns or themes

**Connections Found**:
- Links between memories that weren't explicitly made

**Insights**:
- 1-3 meta-observations about the memory collection

**Recommendations**:
- Suggested memories to create, update, or consolidate

## Example

User: `/glean ai-ready-vault`

Response:
"Gleaning patterns from AI Ready Vault memories...

**Patterns Observed**:
1. Composability is a recurring theme—skills, tiers, teaching approach all emphasize modularity
2. 'Let the work speak' appears in both personal branding and product design decisions
3. Education-first positioning across all strategic decisions

**Connections Found**:
- Teaching pattern (show→problem→conceptual) mirrors the product evolution (demo→pain→solution)
- Skills library concept directly enables the 'incremental adoption' goal

**Insights**:
- The project philosophy is internally consistent—same principles apply at multiple levels
- Strong bias toward empowering users vs. prescribing solutions

**Recommendations**:
- Consider creating a 'design principles' memory that captures the recurring themes
- The composability pattern could become a procedural memory for future projects"

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/robabby) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
