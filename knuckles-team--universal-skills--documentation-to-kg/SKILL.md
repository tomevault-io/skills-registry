---
name: research-discovery-team
description: >- Use when this capability is needed.
metadata:
  author: Knuckles-Team
---

# Documentation To Kg Workflow

**CONCEPT:RESEARCH-001**

Parallel execution workflow for documentation to kg using the Unified Parallel Engine

## Steps

### Step 1: Parse Markdown
**Agent**: `search-agent`
**Tools**: `sx_search, graph_query`

Execute parse markdown operations for the Documentation To Kg workflow.
Expected: `parse_markdown_artifacts`

### Step 2: Extract Concepts [depends_on: parse_markdown]
**Agent**: `analyzer-agent`
**Tools**: `graph_analyze, sx_storage`

Execute extract concepts operations for the Documentation To Kg workflow.
Expected: `extract_concepts_artifacts`

### Step 3: Entity Resolution [depends_on: extract_concepts]
**Agent**: `synthesizer-agent`
**Tools**: `graph_analyze, document_tools`

Execute entity resolution operations for the Documentation To Kg workflow.
Expected: `entity_resolution_artifacts`

### Step 4: Ingest [depends_on: entity_resolution]
**Agent**: `ingestor-agent`
**Tools**: `graph_write, kg_graph_ingest`

Execute ingest operations for the Documentation To Kg workflow.
Expected: `ingest_artifacts`

### Step 5: KG Persistence [depends_on: ingest]
**Agent**: `ingestor-agent`
**Tools**: `graph_write`

Persist workflow results as nodes and edges in the Knowledge Graph.
Create appropriate typed nodes with metadata and link to existing domain entities.

## Output
- Documentation To Kg results persisted in KG
- Structured report (MD/PDF)
- Audit trail with timestamps and agent attributions

---
> Source: [Knuckles-Team/universal-skills](https://github.com/Knuckles-Team/universal-skills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-15 -->
