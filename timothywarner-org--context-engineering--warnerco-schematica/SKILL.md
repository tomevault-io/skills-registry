---
name: warnerco-schematica
description: Develop and extend the WARNERCO Robotics Schematica system - an agentic RAG application with FastAPI, FastMCP, LangGraph orchestration, and 3-tier memory (JSON/Chroma/Azure AI Search). Use when working on the schematica backend, adding schematics, modifying the LangGraph flow, updating dashboards, or deploying to Azure. Use when this capability is needed.
metadata:
  author: timothywarner-org
---

# WARNERCO Robotics Schematica

Agentic robot schematics system with semantic memory and retrieval-augmented generation.

## Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                     FastAPI + FastMCP                       │
├─────────────────────────────────────────────────────────────┤
│  LangGraph Flow (7-node Hybrid RAG)                         │
│  parse_intent -> query_graph -> inject_scratchpad -> retrieve│
│  -> compress -> reason -> respond                            │
├─────────────────────────────────────────────────────────────┤
│  Hybrid Memory Layer                                        │
│  +-------------------+  +-------------------+  +-----------+│
│  | Vector Store      |  | Graph Store       |  | Scratchpad|│
│  | JSON->Chroma->    |  | SQLite + NetworkX |  | In-memory |│
│  | Azure AI Search   |  | (Knowledge Graph) |  | (Session) |│
│  +-------------------+  +-------------------+  +-----------+│
└─────────────────────────────────────────────────────────────┘
```

## Project Structure

```
src/warnerco/backend/
├── app/
│   ├── main.py           # FastAPI application
│   ├── config.py         # Settings and environment
│   ├── models.py         # Pydantic schemas
│   ├── routes.py         # API endpoints
│   ├── mcp_tools.py      # FastMCP tool definitions
│   ├── adapters/         # Memory backend implementations
│   │   ├── json_store.py
│   │   ├── chroma_store.py
│   │   ├── azure_search_store.py
│   │   ├── graph_store.py
│   │   └── scratchpad_store.py
│   └── langgraph/
│       └── flow.py       # 7-node hybrid RAG orchestration
├── data/
│   ├── schematics/       # JSON source of truth
│   └── chroma/           # Vector embeddings
├── static/dash/          # SPA dashboards
└── .env                  # Configuration
```

## Commands

```bash
cd src/warnerco/backend

# Local development
uv sync
uv run uvicorn app.main:app --reload --port 8000

# Index schematics into Chroma
uv run python -c "from app.adapters.chroma_store import ChromaMemoryStore; import asyncio; asyncio.run(ChromaMemoryStore().index_all())"

# MCP stdio server (for Claude Desktop)
uv run warnerco-mcp
```

## Memory Backend Selection

Set `MEMORY_BACKEND` in `.env`:

| Backend | Use Case | Config |
|---------|----------|--------|
| `json` | Fastest startup, keyword search | Default |
| `chroma` | Local semantic search | Recommended for dev |
| `azure_search` | Enterprise deployment | Requires Azure resources |

## MCP Tools

| Tool | Description |
|------|-------------|
| `warn_list_robots` | List schematics with filters |
| `warn_get_robot` | Get schematic by ID |
| `warn_semantic_search` | Natural language search |
| `warn_memory_stats` | Backend statistics |
| `warn_add_relationship` | Create graph triplet (subject, predicate, object) |
| `warn_graph_neighbors` | Get connected entities |
| `warn_graph_path` | Find shortest path between entities |
| `warn_graph_stats` | Graph node/edge statistics |
| `warn_scratchpad_write` | Store session observation |
| `warn_scratchpad_read` | Retrieve session entries |
| `warn_scratchpad_clear` | Clear session entries |
| `warn_scratchpad_stats` | Token budget statistics |

## LangGraph Flow

7-node hybrid retrieval-augmented generation:

1. **parse_intent** - Classify query (lookup/diagnostic/analytics/search)
2. **query_graph** - Enrich with knowledge graph relationships
3. **inject_scratchpad** - Add session working memory
4. **retrieve** - Fetch candidates from memory backend
5. **compress_context** - Minimize token bloat
6. **reason** - LLM generates response (Azure OpenAI gpt-4o-mini)
7. **respond** - Format for dashboards/MCP

## Adding Schematics

Edit `data/schematics/schematics.json`:

```json
{
  "id": "WRN-00026",
  "model": "WC-900",
  "name": "New Robot Name",
  "component": "component description",
  "version": "v1.0",
  "summary": "Technical summary...",
  "category": "sensors",
  "status": "active",
  "tags": ["tag1", "tag2"],
  "specifications": {
    "spec_key": "spec_value"
  },
  "url": "https://schematics.warnerco.io/..."
}
```

Then re-index: `uv run python -c "...index_all()"`

## Dashboards

- **Schematics Browser** (`/dash/schematics/`) - Search, filter, view robot data
- **Memory Learning** (`/dash/memory/`) - Educational RAG visualization

## Azure Deployment

See `references/azure-deployment.md` for:
- Container App setup
- APIM configuration
- AI Search indexing
- OpenAI model deployment

## API Endpoints

| Method | Path | Description |
|--------|------|-------------|
| GET | `/api/robots` | List schematics |
| GET | `/api/robots/{id}` | Get by ID |
| POST | `/api/search` | Semantic search |
| GET | `/api/memory/stats` | Backend stats |
| GET | `/docs` | OpenAPI docs |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/timothywarner-org) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
