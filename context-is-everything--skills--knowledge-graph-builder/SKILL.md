---
name: knowledge-graph-builder
description: >- Use when this capability is needed.
metadata:
  author: context-is-everything
---

# 🕸️ Knowledge Graph Builder

Extract structured knowledge graphs from any text-based knowledge source.

## Overview

Transform unstructured text into structured knowledge graph JSON format, enabling entity relationship mapping, timeline validation, fact verification, and graph visualization compatible with D3.js, Neo4j, and other graph tools.

## When to Use This Skill

Use this skill when you need to:
- Extract entities and relationships from documents, reports, or notes
- Build knowledge graphs for visualization
- Validate timelines and chronological consistency
- Link claims to supporting quotes and evidence
- Prepare data for graph database import (Neo4j, etc.)
- Create interactive network visualizations

## Input Sources

Works with **any text-based knowledge source**:

**Structured Documents**: Interview transcripts, meeting notes, research reports, case studies

**Semi-Structured Data**: Email threads, chat logs, wiki pages, knowledge base articles

**Unstructured Text**: Articles, essays, books, archived documents, legacy knowledge bases

## Workflow

### 1. Read Source Material

Load your knowledge source(s) and note the source type and metadata.

### 2. Extract Entities

Identify key entities with confidence scoring:
- **Person**: Individuals (names, roles, relationships)
- **Organization**: Companies, institutions, groups
- **Place**: Locations, offices, geographic references
- **Concept**: Themes, topics, abstract ideas
- **Claim**: Factual assertions requiring verification

See [references/entity-extraction.md](references/entity-extraction.md) for detailed patterns.

### 3. Identify Relationships

Map connections between entities using typed relationships (works_for, reports_to, collaborated_with, located_in, mentioned_in, etc.).

See [references/relationship-types.md](references/relationship-types.md) for complete taxonomy.

### 4. Extract Quotes & Evidence

Capture verbatim text with:
- Speaker/author attribution
- Temporal context (when)
- Spatial context (where)
- Sentiment analysis
- Confidence scoring

### 5. Link Claims to Evidence

Identify factual assertions, map supporting evidence (quote IDs), and calculate claim confidence based on evidence strength.

### 6. Validate & Quality Check

Run validation checks:
- ID uniqueness across all entities
- Referential integrity (all IDs resolve)
- Timeline consistency
- Evidence completeness (all relationships have quotes)
- No orphaned nodes

See [references/validation-checklist.md](references/validation-checklist.md) for complete QA process.

### 7. Generate Outputs

Create visualization-ready files:
1. `{name}-knowledge-graph.json` - Structured graph data
2. `{name}-graph-viewer.html` - Interactive visualization (optional)
3. `{name}-validation-report.md` - Quality metrics

## Output Format

JSON conforming to knowledge graph schema:

```json
{
  "source": {
    "title": "Knowledge Source Title",
    "source_type": "document|transcript|report|article",
    "extracted_at_iso": "2026-01-31T00:00:00Z"
  },
  "entities": [/* people, orgs, places, concepts with aliases */],
  "themes": [/* recurring topics with keywords */],
  "quotes": [/* verbatim text with context and confidence */],
  "claims": [/* assertions with evidence */],
  "relationships": [/* typed connections with evidence */]
}
```

See [examples/sample-output.json](examples/sample-output.json) for complete anonymized example.

## Output Location

Save files to project directory:
`/home/sasha/all-project-files/deployed-md-files/docs/{project-name}/`

## Confidence Scoring

All entities, relationships, quotes, and claims include confidence scores (0.0-1.0):

**Scoring Factors**:
1. Mention frequency - How often mentioned
2. Context clarity - Explicitly stated vs implied
3. Evidence strength - Supporting quotes available
4. Disambiguation - Unique vs ambiguous references
5. Source quality - Authoritative vs uncertain

**Confidence Ranges**:
- `0.90-1.00` - High confidence, multiple clear references
- `0.75-0.89` - Good confidence, explicit mention
- `0.60-0.74` - Moderate confidence, some ambiguity
- `0.40-0.59` - Low confidence, inferred or unclear
- `<0.40` - Very uncertain, flag for review

## Visualization Integration

The knowledge graph JSON integrates with the `cool-charts` skill for visualization:

**Interactive Network Graph**: Force-directed layout, color/size-coded, filterable

**Timeline View**: Chronological progression, duration bars, quote markers

See the `cool-charts` skill for complete visualization options.

## Quality Standards

**Schema Compliance**: All required fields, correct data types, valid enum values

**Evidence Requirements**: Every relationship has evidence_quote_ids, all IDs reference existing objects, quotes are verbatim

**Timeline Integrity**: Chronologically consistent, no contradictions, temporal contexts align

**Entity Normalization**: Aliases captured, no duplicates, clear disambiguation

**Graph Completeness**: All entities have ≥1 relationship, no orphaned nodes, visualization-ready

## Common Use Cases

**Career/Professional Analysis**: Extract people, companies, roles; map employment, reporting, collaboration; validate timeline

**Research Documentation**: Extract authors, concepts, institutions; map citations, collaboration, influence; link claims to evidence

**Business Intelligence**: Extract companies, products, markets; map partnerships, competition, acquisitions; track changes

**Knowledge Base Migration**: Extract structured data from legacy docs; preserve relationships and context; enable graph database import

## Reference Documentation

**Detailed extraction patterns**: [references/entity-extraction.md](references/entity-extraction.md)

**Relationship taxonomy**: [references/relationship-types.md](references/relationship-types.md)

**Quality assurance**: [references/validation-checklist.md](references/validation-checklist.md)

**JSON schema**: [references/schema.md](references/schema.md)

## Troubleshooting

**Ambiguous entity references**: Use alias system, assign lower confidence, check context

**Missing temporal context**: Keep time_text as-is if relative, set time_iso to null, flag with certainty: "uncertain"

**Too many potential entities**: Focus on frequently mentioned (3+ references), prioritize entities with clear relationships

**Orphaned entities**: Only include entities with ≥1 relationship, document isolated mentions in notes

**No evidence for relationship**: Search for implicit evidence, mark as inferred if contextual, don't create if no evidence

## Success Criteria

**Functional**: JSON validates, all IDs unique, all relationships have evidence, quotes are verbatim

**Quality**: Key entities identified with aliases, themes represented, timeline consistent, sentiment/certainty scored

**Integration**: Visualization-ready JSON, compatible with cool-charts skill, importable to graph databases

---

**Skill Version**: 2.0 (Generalized)
**Created**: 2026-01-31
**Based On**: quote-to-knowledge-graph-extraction v1.0

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/context-is-everything) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
