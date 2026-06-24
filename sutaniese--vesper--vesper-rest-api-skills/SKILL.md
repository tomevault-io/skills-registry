---
name: vesper-rest-api-skills
description: REST-based Vesper agent skills for dataset search, download, cleaning, quality analysis, and export. Includes OpenAI function-calling schema, LangChain tools, and Claude tool-use format. Use when this capability is needed.
metadata:
  author: sutaniese
---

# Vesper REST API Skills

A ready-to-use skill set that calls Vesper via REST API and returns structured JSON.

## Environment

**Self-hosted (recommended today):** run the MCP server in HTTP mode (`vespermcp --http`, default port `8787`), then point clients at `http://127.0.0.1:8787` and use `POST /v1/tools/call` with the same tool names as MCP (see `@vespermcp/mcp-server` docs / `docs/configuration.md`).

Set these environment variables before using the **skill script** helpers (which still assume path-style endpoints unless you override them):

```bash
export VESPER_API_URL="https://your-vesper-host"
export VESPER_API_KEY="vesper_sk_..."
```

Auth header used for every request:

```text
Authorization: Bearer VESPER_API_KEY
```

## Skill Set

1. `search_datasets` - Find datasets by query.
2. `download_dataset` - Download dataset by ID.
3. `clean_dataset` - Run cleaning pipeline.
4. `analyze_quality` - Return quality report.
5. `export_dataset` - Export dataset to target format.

## Endpoints

Default endpoint mapping:

- `search_datasets` -> `/api/search_datasets`
- `download_dataset` -> `/api/download_dataset`
- `clean_dataset` -> `/api/clean_dataset`
- `analyze_quality` -> `/api/analyze_quality`
- `export_dataset` -> `/api/export_dataset`

You can override each endpoint via env vars:

- `VESPER_ENDPOINT_SEARCH_DATASETS`
- `VESPER_ENDPOINT_DOWNLOAD_DATASET`
- `VESPER_ENDPOINT_CLEAN_DATASET`
- `VESPER_ENDPOINT_ANALYZE_QUALITY`
- `VESPER_ENDPOINT_EXPORT_DATASET`

## File

Implementation is in [vesper-rest-skills.js](.agents/skills/vesper-rest-api/vesper-rest-skills.js).

## Usage

```js
import {
  createVesperSkills,
  createOpenAIFunctions,
  createClaudeTools,
  createLangChainTools,
  createLangChainDynamicStructuredTools,
} from "./.agents/skills/vesper-rest-api/vesper-rest-skills.js";

const skills = createVesperSkills();
const search = skills.find((s) => s.name === "search_datasets");
const result = await search.invoke({ query: "medical imaging", limit: 5 });
console.log(result);
```

### OpenAI Function Calling

```js
const openAIFunctions = createOpenAIFunctions();
```

Returns:

- `type: "function"`
- function `name`
- function `description`
- JSON-schema `parameters`

### Claude Tool Use

```js
const claudeTools = createClaudeTools();
```

Returns:

- tool `name`
- tool `description`
- `input_schema`

### LangChain Tools

```js
const langchainTools = createLangChainTools();
```

Each tool object includes:

- `name`
- `description`
- Zod `schema`
- `invoke(input)` -> structured JSON

If you want native LangChain `DynamicStructuredTool` objects:

```js
const dynamicTools = await createLangChainDynamicStructuredTools();
```

## Structured JSON Output

All skill calls return a consistent JSON envelope:

```json
{
  "skill": "search_datasets",
  "status": "success",
  "...": "endpoint-specific payload"
}
```

---
> Source: [sutaniese/Vesper](https://github.com/sutaniese/Vesper) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-15 -->
