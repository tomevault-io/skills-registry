---
name: flexible-data-importer
description: id: flexible-data-importer Use when this capability is needed.
metadata:
  author: openclaw
---
# OpenClaw Data Importer Skill

<!-- SKILL-META
id: flexible-data-importer
version: 1.0.0
author: OpenClaw
description: AI-driven data ingestion for CSV, JSON, XLSX with auto-schema generation and Supabase integration.
capabilities:
  - data-ingestion
  - schema-generation
  - supabase
  - etl
requires:
  llm: true
  filesystem: true
  network: true
invocation:
  cli: data-importer <file-path>
  api: UniversalImporter.execute(filePath)
parameters:
  - name: filePath
    type: string
    required: true
    description: Path to the source file (CSV, JSON, XLSX).
-->

An AI-driven skill that ingests disparate data formats (CSV, JSON, XLSX) and builds a structured Supabase database. It automatically infers relationships, types, and schema names.

## Inputs
* `filePath`: String - Path to the source file.
* `supabaseUrl`: String - Your project URL.
* `supabaseKey`: String - Service role key for schema creation.

## Capabilities
- **Auto-Schema**: No need to define tables beforehand.
- **Type Safety**: Automatically converts strings to dates/numbers where appropriate.
- **Batched Uploads**: Handles large historical datasets without crashing.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/openclaw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
