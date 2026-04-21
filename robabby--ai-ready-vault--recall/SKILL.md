---
name: recall
description: Search the AI-ready vault memory system by keyword or concept. Use when needing to retrieve previously stored information, find past decisions, or locate relevant context from earlier sessions. Use when this capability is needed.
metadata:
  author: robabby
---

# Recall

Search memories by keyword and concept.

## Workflow

1. Parse search terms from $ARGUMENTS
   - If none provided, ask user what to search for

2. Search memory files
   - Search frontmatter `concepts` field
   - Search file content
   - Search filenames

3. Filter and rank results
   - Prioritize by importance score
   - Prioritize by recency
   - Prioritize by concept match strength

4. Present findings
   - Show relevant memories with summaries
   - Include file paths for reference
   - Note memory type and importance

## Search Locations

```
Areas/AI/Memory/
├── Episodic/    # Events, experiences
├── Semantic/    # Facts, knowledge
├── Procedural/  # Patterns, how-tos
└── Strategic/   # Decisions, plans
```

## Parameters

- `$ARGUMENTS` (required): Search terms or concepts to find

## Default Paths

Searches the memory system at: `Areas/AI/Memory/`

## Related Skills

- `/remember` - Store new memories
- `/hydrate` - Load recent relevant memories at session start
- `/glean` - Discover patterns across memories

## Search Patterns

```bash
# Content search
Grep pattern="{terms}" path="Areas/AI/Memory" glob="*.md"

# Type-filtered search
Grep pattern="{terms}" path="Areas/AI/Memory/Strategic" glob="*.md"
```

## Output Format

For each relevant memory found:
- **Title** (linked to file)
- **Type** and **Importance**
- **Key concepts**
- **Brief summary** (2-3 sentences)

## Example

User: `/recall teaching patterns`

Response:
"Found 2 relevant memories:

1. **Teaching Pattern for AI Concepts** (Procedural, 0.6)
   Concepts: teaching, education, show-dont-tell, ux-design
   Pattern: Show → Problem → Conceptual. Lead with demos that create wonder, name the pain, then layer in principles.
   Path: `Areas/AI/Memory/Procedural/2025-01-08 - Teaching Pattern for AI Concepts.md`

2. **AI Ready Vault Flagship Guide Design** (Strategic, 0.8)
   Concepts: ai-ready-vault, guide, scroll-experience
   The interactive scroll guide uses show-don't-tell with terminal demos.
   Path: `Areas/AI/Memory/Strategic/2025-01-08 - AI Ready Vault Product Vision.md`"

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/robabby) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
