---
name: ragflow
description: Ragflow API key (use least-privilege key, can manage datasets/upload files) Use when this capability is needed.
metadata:
  author: openclaw
---

# Ragflow API Client

Universal client for Ragflow — self-hosted RAG (Retrieval-Augmented Generation) platform.

## Features

- **Dataset management** — Create, list, delete knowledge bases
- **Document upload** — Upload files or text content
- **Chat queries** — Run RAG queries against datasets
- **Chunk management** — Trigger parsing, list chunks

## Usage

```bash
# List datasets
node {baseDir}/scripts/ragflow.js datasets

# Create dataset
node {baseDir}/scripts/ragflow.js create-dataset --name "My Knowledge Base"

# Upload document
node {baseDir}/scripts/ragflow.js upload --dataset DATASET_ID --file article.md

# Chat query
node {baseDir}/scripts/ragflow.js chat --dataset DATASET_ID --query "What is stroke?"

# List documents in dataset
node {baseDir}/scripts/ragflow.js documents --dataset DATASET_ID
```

## Configuration

Set environment variables in your `.env`:

```bash
RAGFLOW_URL=https://your-ragflow-instance.com
RAGFLOW_API_KEY=your-api-key
```

## API

This skill wraps Ragflow's REST API:

- `GET /api/v1/datasets` — List datasets
- `POST /api/v1/datasets` — Create dataset
- `DELETE /api/v1/datasets/{id}` — Delete dataset
- `POST /api/v1/datasets/{id}/documents` — Upload document
- `POST /api/v1/datasets/{id}/chunks` — Trigger parsing
- `POST /api/v1/datasets/{id}/retrieval` — RAG query

Full API docs: https://ragflow.io/docs

## Examples

```javascript
// Programmatic usage
const ragflow = require('{baseDir}/lib/api.js');

// Upload and parse
await ragflow.uploadDocument(datasetId, './article.md', { filename: 'article.md' });
await ragflow.triggerParsing(datasetId, [documentId]);

// Query
const answer = await ragflow.chat(datasetId, 'What are the stroke guidelines?');
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/openclaw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
