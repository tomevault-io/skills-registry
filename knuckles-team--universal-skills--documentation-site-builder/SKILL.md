---
name: content-creation-team
description: >- Use when this capability is needed.
metadata:
  author: Knuckles-Team
---

# Documentation Site Builder Workflow

**CONCEPT:SOCIAL-001**

Parallel execution workflow for documentation site builder using the Unified Parallel Engine

## Steps

### Step 1: Extract From Code
**Agent**: `content-creator`
**Tools**: `graph_query, document_tools`

Execute extract from code operations for the Documentation Site Builder workflow.
Expected: `extract_from_code_artifacts`

### Step 2: Write Docs [depends_on: extract_from_code]
**Agent**: `media-processor`
**Tools**: `graph_analyze`

Execute write docs operations for the Documentation Site Builder workflow.
Expected: `write_docs_artifacts`

### Step 3: Generate Api Ref [depends_on: write_docs]
**Agent**: `publisher-agent`
**Tools**: `graph_write`

Execute generate api ref operations for the Documentation Site Builder workflow.
Expected: `generate_api_ref_artifacts`

### Step 4: Deploy [depends_on: generate_api_ref]
**Agent**: `analytics-agent`
**Tools**: `graph_query, graph_analyze`

Execute deploy operations for the Documentation Site Builder workflow.
Expected: `deploy_artifacts`

### Step 5: KG Persistence [depends_on: deploy]
**Agent**: `analytics-agent`
**Tools**: `graph_write`

Persist workflow results as nodes and edges in the Knowledge Graph.
Create appropriate typed nodes with metadata and link to existing domain entities.

## Output
- Documentation Site Builder results persisted in KG
- Structured report (MD/PDF)
- Audit trail with timestamps and agent attributions

---
> Source: [Knuckles-Team/universal-skills](https://github.com/Knuckles-Team/universal-skills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-15 -->
