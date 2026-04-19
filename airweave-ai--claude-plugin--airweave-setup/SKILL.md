---
name: airweave-setup
description: Set up and integrate Airweave into applications. Use when the user wants to install Airweave, create collections, connect data sources, configure MCP servers, or set up the SDK to integrate Airweave into their app. For developers building with Airweave. Use when this capability is needed.
metadata:
  author: airweave-ai
---

# Airweave Setup & Integration

Airweave is an open-source platform that makes any app searchable for AI agents. It connects to apps, productivity tools, databases, or document stores and transforms their contents into searchable knowledge bases.

## Quick Start

### Option 1: Airweave Cloud (Recommended)

1. Sign up at https://app.airweave.ai
2. Get your API key from the dashboard
3. Install the SDK:

```bash
pip install airweave-sdk   # Python
npm install @airweave/sdk  # TypeScript
```

### Option 2: Self-Hosted

```bash
git clone https://github.com/airweave-ai/airweave.git
cd airweave
chmod +x start.sh
./start.sh
```

Access the dashboard at http://localhost:8080

## Core Workflow

The typical Airweave workflow follows these steps:

### 1. Create a Collection

A collection groups multiple data sources into a single searchable endpoint.

**Python:**
```python
from airweave import AirweaveSDK

client = AirweaveSDK(
    api_key="YOUR_API_KEY",
    base_url="https://api.airweave.ai"  # or http://localhost:8001 for self-hosted
)

collection = client.collections.create(name="My Knowledge Base")
print(f"Collection ID: {collection.readable_id}")
```

**TypeScript:**
```typescript
import { AirweaveSDKClient } from "@airweave/sdk";

const client = new AirweaveSDKClient({ apiKey: "YOUR_API_KEY" });
const collection = await client.collections.create({ name: "My Knowledge Base" });
```

### 2. Add Source Connections

Connect data sources to your collection. 40+ sources supported including:
- **Productivity**: Notion, Google Drive/Docs/Slides, Dropbox, OneDrive, SharePoint, Box, Airtable
- **Communication**: Slack, Gmail, Outlook, Teams, Google Calendar
- **Project Management**: Jira, Linear, Asana, Trello, Monday, ClickUp, Todoist
- **Development**: GitHub, GitLab, Bitbucket, Confluence
- **CRM & Sales**: Salesforce, HubSpot, Attio, Zendesk, Pipedrive, Shopify
- **Data**: Stripe, PostgreSQL

See [SDK-REFERENCE.md](SDK-REFERENCE.md) for the complete list of source short names.

**Python (API Key sources like Stripe, Linear):**
```python
source = client.source_connections.create(
    name="My Stripe Connection",
    short_name="stripe",
    readable_collection_id=collection.readable_id,
    authentication={
        "credentials": {"api_key": "sk_live_your_stripe_key"}
    }
)
```

**OAuth sources (Slack, Google, Microsoft, etc.):**

Most sources use OAuth for authentication. Use the Airweave UI at https://app.airweave.ai to connect these sources—it handles the OAuth flow automatically.

### 3. Search Your Data

Once synced, search across all connected sources. Airweave provides three search modes:

| Mode | Description | Best For |
|------|-------------|----------|
| `instant` | Direct vector search | Fast lookups, browsing |
| `classic` | AI-optimized search with LLM-generated search plans | Most searches (default) |
| `agentic` | Full agent loop with iterative reasoning | Complex questions, analysis |

**Python:**
```python
# Classic search (default) — AI-optimized
results = client.collections.search(
    readable_id=collection.readable_id,
    query="customer feedback about pricing",
    mode="classic"
)

for result in results.results:
    print(f"Source: {result.airweave_system_metadata.source_name}")
    print(f"Name: {result.name}")
    print(f"Content: {result.textual_representation[:200]}...")
    print(f"Score: {result.relevance_score}")
    print(f"URL: {result.web_url}")
```

## Search Modes in Detail

### Instant Search

Fast, direct vector search. Best for simple lookups.

```python
results = client.collections.search(
    readable_id=collection.readable_id,
    query="API documentation",
    mode="instant",
    retrieval_strategy="hybrid",  # "hybrid" (default), "neural", or "keyword"
    limit=20,
    offset=0
)
```

### Classic Search

AI generates an optimized search plan. Best general-purpose mode.

```python
results = client.collections.search(
    readable_id=collection.readable_id,
    query="technical documentation",
    mode="classic",
    limit=20,
    offset=0
)
```

### Agentic Search

Full agent loop that iteratively searches, reads, and reasons over results.

```python
results = client.collections.search(
    readable_id=collection.readable_id,
    query="Summarize all decisions about the authentication redesign",
    mode="agentic",
    thinking=True,  # Enable extended reasoning
    limit=10
)
```

## MCP Integration for AI Agents

Airweave exposes search via MCP (Model Context Protocol) for seamless AI agent integration.

### Setup for Claude Desktop / Cursor

Add to your MCP configuration (`~/.cursor/mcp.json`):

```json
{
  "mcpServers": {
    "airweave-search": {
      "command": "npx",
      "args": ["airweave-mcp-search"],
      "env": {
        "AIRWEAVE_API_KEY": "your-api-key",
        "AIRWEAVE_COLLECTION": "your-collection-id",
        "AIRWEAVE_BASE_URL": "https://api.airweave.ai"
      }
    }
  }
}
```

### Hosted MCP Server

For cloud-based AI platforms like OpenAI Agent Builder:
- URL: `https://mcp.airweave.ai`
- Uses Streamable HTTP transport (MCP 2025-03-26)

See [MCP-SETUP.md](MCP-SETUP.md) for detailed configuration.

## Troubleshooting

### No Results Found
- Check that sync has completed (can take a few minutes for large sources)
- Verify the collection ID is correct
- Try a broader search query
- Try a different search mode (e.g., `classic` instead of `instant`)

### Authentication Errors
- Verify your API key is valid
- Check that the API key has access to the collection
- For OAuth sources, the token may have expired—reconnect in the UI

### Rate Limits
- The API has rate limits for protection
- Implement exponential backoff for retries
- Contact support for higher limits

## Additional Resources

- **Documentation**: https://docs.airweave.ai
- **API Reference**: https://api.airweave.ai/docs (Swagger)
- **GitHub**: https://github.com/airweave-ai/airweave
- **Discord**: https://discord.gg/484HY9Ehxt

For detailed SDK reference, see [SDK-REFERENCE.md](SDK-REFERENCE.md).
For advanced search patterns, see [SEARCH-PATTERNS.md](SEARCH-PATTERNS.md).

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/airweave-ai) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
