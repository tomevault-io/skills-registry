---
name: azure-search-rest
description: Expert guidance for interacting with Azure AI Search using the REST API. Use this skill when you need to create, manage, or query search indexes, knowledge bases, knowledge sources, upload documents, perform full-text/vector/hybrid searches, or implement agentic retrieval via REST. Covers data plane operations for the 2025-11-01-preview API version. Use when this capability is needed.
metadata:
  author: mattgotteiner
---

# Azure AI Search REST API Expert

This skill provides comprehensive guidance for interacting with Azure AI Search using the REST API. Azure AI Search is a fully managed, cloud-hosted service that connects your data to AI, supporting classic search (single index queries) and agentic retrieval (multi-source, LLM-assisted retrieval for agents and chatbots).

## Prerequisites

- An Azure AI Search service deployed in your Azure subscription
- Authentication via either:
  - **API Key**: Admin key (full access) or Query key (read-only)
  - **OAuth2/Bearer Token**: Microsoft Entra ID authentication
- Use the [azure-cli skill](../azure-cli/SKILL.md) to obtain bearer tokens
- Use the [rest-http-caller skill](../rest-http-caller/SKILL.md) to execute REST requests

## API Base URL

All requests are made to:
```
https://[service-name].search.windows.net
```

Replace `[service-name]` with your Azure Search service name.

## Current API Version

```
api-version=2025-11-01-preview
```

Note: Always append `?api-version=2025-11-01-preview` to your requests.

## Authentication

### Option 1: API Key Authentication

Include the `api-key` header in all requests:

```bash
--header "api-key: YOUR_ADMIN_OR_QUERY_KEY"
```

### Option 2: Bearer Token Authentication (OAuth2)

Use the [azure-cli skill](../azure-cli/SKILL.md) to get a bearer token:

```bash
# Get token for Azure Search
$TOKEN = az account get-access-token --resource https://search.azure.com --query accessToken --output tsv

# Use in requests
--header "Authorization: Bearer $TOKEN"
```

---

## Part 1: Classic Search - Index Operations

Classic search is an index-first retrieval model for predictable, low-latency queries targeting a single, predefined search index.

### 1.1 Create or Update Index

Creates a new search index or updates an existing one. Indexes define the structure of searchable content.

**Endpoint:**
```
PUT https://[service-name].search.windows.net/indexes('{indexName}')?api-version=2025-11-01-preview
```

**Required Headers:**
- `Content-Type: application/json`
- `api-key: [admin-key]` or `Authorization: Bearer [token]`
- `Accept: application/json;odata.metadata=minimal`
- `Prefer: return=representation`

**Example - Create a Simple Index:**

```bash
dotnet run ../rest-http-caller/HttpCaller.cs -- PUT "https://YOUR_SERVICE.search.windows.net/indexes('hotels-sample')?api-version=2025-11-01-preview" \
  --header "Content-Type: application/json" \
  --header "api-key: YOUR_ADMIN_KEY" \
  --header "Accept: application/json;odata.metadata=minimal" \
  --header "Prefer: return=representation" \
  --body '{
    "name": "hotels-sample",
    "fields": [
      {"name": "HotelId", "type": "Edm.String", "key": true, "filterable": true, "sortable": true},
      {"name": "HotelName", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true},
      {"name": "Description", "type": "Edm.String", "searchable": true, "analyzer": "en.lucene"},
      {"name": "Category", "type": "Edm.String", "searchable": true, "filterable": true, "facetable": true},
      {"name": "Tags", "type": "Collection(Edm.String)", "searchable": true, "filterable": true, "facetable": true},
      {"name": "Rating", "type": "Edm.Double", "filterable": true, "sortable": true, "facetable": true},
      {"name": "LastRenovationDate", "type": "Edm.DateTimeOffset", "filterable": true, "sortable": true}
    ]
  }'
```

**Example - Create Index with Vector Search:**

> **Note:** The `hnswParameters` object is optional. Azure Search uses sensible defaults, so only include these parameters if the user specifically requests custom tuning for `m`, `efConstruction`, `efSearch`, or `metric`.

```bash
dotnet run ../rest-http-caller/HttpCaller.cs -- PUT "https://YOUR_SERVICE.search.windows.net/indexes('vector-sample')?api-version=2025-11-01-preview" \
  --header "Content-Type: application/json" \
  --header "api-key: YOUR_ADMIN_KEY" \
  --header "Accept: application/json;odata.metadata=minimal" \
  --header "Prefer: return=representation" \
  --body '{
    "name": "vector-sample",
    "fields": [
      {"name": "id", "type": "Edm.String", "key": true, "sortable": true},
      {"name": "title", "type": "Edm.String", "searchable": true},
      {"name": "content", "type": "Edm.String", "searchable": true},
      {"name": "contentVector", "type": "Collection(Edm.Single)", "searchable": true, "retrievable": true, "dimensions": 1536, "vectorSearchProfile": "vector-profile"}
    ],
    "vectorSearch": {
      "algorithms": [
        {"name": "hnsw-config", "kind": "hnsw"}
      ],
      "profiles": [
        {"name": "vector-profile", "algorithm": "hnsw-config", "vectorizer": "openai-vectorizer"}
      ],
      "vectorizers": [
        {"name": "openai-vectorizer", "kind": "azureOpenAI", "azureOpenAIParameters": {"resourceUri": "https://YOUR_OPENAI.openai.azure.com", "deploymentId": "text-embedding-3-large", "modelName": "text-embedding-3-large"}}
      ]
    }
  }'
```

**Example - Create Index with Semantic Configuration:**

Semantic configurations enable semantic ranking for improved relevance. Use `prioritizedContentFields` (not `contentFields`) for the 2025-11-01-preview API.

```bash
dotnet run ../rest-http-caller/HttpCaller.cs -- PUT "https://YOUR_SERVICE.search.windows.net/indexes('semantic-sample')?api-version=2025-11-01-preview" \
  --header "Content-Type: application/json" \
  --header "api-key: YOUR_ADMIN_KEY" \
  --header "Accept: application/json;odata.metadata=minimal" \
  --header "Prefer: return=representation" \
  --body '{
    "name": "semantic-sample",
    "fields": [
      {"name": "id", "type": "Edm.String", "key": true},
      {"name": "title", "type": "Edm.String", "searchable": true},
      {"name": "content", "type": "Edm.String", "searchable": true},
      {"name": "category", "type": "Edm.String", "searchable": true, "filterable": true}
    ],
    "semantic": {
      "configurations": [
        {
          "name": "my-semantic-config",
          "prioritizedFields": {
            "titleField": {"fieldName": "title"},
            "prioritizedContentFields": [
              {"fieldName": "content"}
            ],
            "prioritizedKeywordsFields": [
              {"fieldName": "category"}
            ]
          }
        }
      ]
    }
  }'
```

**Semantic Configuration Fields:**
| Field | Description |
|-------|-------------|
| `titleField` | Single field for document title (optional, set to `null` if not applicable) |
| `prioritizedContentFields` | Array of content fields for semantic ranking (most important first) |
| `prioritizedKeywordsFields` | Array of keyword fields for semantic ranking (optional) |

> **Note:** Do NOT use `contentFields` - this is an invalid property name. Always use `prioritizedContentFields`.

**Field Data Types:**
| Type | Description |
|------|-------------|
| `Edm.String` | Text field |
| `Edm.Int32`, `Edm.Int64` | Integer fields |
| `Edm.Double` | Floating-point number |
| `Edm.Boolean` | True/false |
| `Edm.DateTimeOffset` | Date and time |
| `Edm.GeographyPoint` | Geographic coordinates |
| `Collection(Edm.String)` | Array of strings |
| `Collection(Edm.Single)` | Vector field (for embeddings) |

**Field Attributes:**
| Attribute | Description |
|-----------|-------------|
| `key` | Unique document identifier (exactly one field must be key) |
| `searchable` | Full-text searchable |
| `filterable` | Can be used in $filter expressions |
| `sortable` | Can be used in $orderby expressions |
| `facetable` | Can be used for faceted navigation |
| `retrievable` | Returned in search results |

### 1.2 List Indexes

**Endpoint:**
```
GET https://[service-name].search.windows.net/indexes?api-version=2025-11-01-preview
```

**Example:**
```bash
dotnet run ../rest-http-caller/HttpCaller.cs -- GET "https://YOUR_SERVICE.search.windows.net/indexes?api-version=2025-11-01-preview" \
  --header "api-key: YOUR_ADMIN_KEY"
```

### 1.3 Get Index

**Endpoint:**
```
GET https://[service-name].search.windows.net/indexes('{indexName}')?api-version=2025-11-01-preview
```

**Example:**
```bash
dotnet run ../rest-http-caller/HttpCaller.cs -- GET "https://YOUR_SERVICE.search.windows.net/indexes('hotels-sample')?api-version=2025-11-01-preview" \
  --header "api-key: YOUR_ADMIN_KEY"
```

### 1.4 Delete Index

**Endpoint:**
```
DELETE https://[service-name].search.windows.net/indexes('{indexName}')?api-version=2025-11-01-preview
```

**Example:**
```bash
dotnet run ../rest-http-caller/HttpCaller.cs -- DELETE "https://YOUR_SERVICE.search.windows.net/indexes('hotels-sample')?api-version=2025-11-01-preview" \
  --header "api-key: YOUR_ADMIN_KEY"
```

---

## Part 2: Document Operations

### 2.1 Upload/Index Documents

Upload, merge, or delete documents in an index. Use batching for efficiency (up to 1000 documents or 16 MB per batch).

**Endpoint:**
```
POST https://[service-name].search.windows.net/indexes('{indexName}')/docs/index?api-version=2025-11-01-preview
```

**Document Actions:**
| Action | Effect |
|--------|--------|
| `upload` | Insert if new, replace if exists (upsert) |
| `merge` | Update existing document fields, fails if not found |
| `mergeOrUpload` | Merge if exists, upload if new |
| `delete` | Remove document from index |

**Example - Upload Documents:**

```bash
dotnet run ../rest-http-caller/HttpCaller.cs -- POST "https://YOUR_SERVICE.search.windows.net/indexes('hotels-sample')/docs/index?api-version=2025-11-01-preview" \
  --header "Content-Type: application/json" \
  --header "api-key: YOUR_ADMIN_KEY" \
  --body '{
    "value": [
      {
        "@search.action": "upload",
        "HotelId": "1",
        "HotelName": "Secret Point Motel",
        "Description": "The hotel is ideally located on the main commercial artery of the city.",
        "Category": "Boutique",
        "Tags": ["pool", "air conditioning", "concierge"],
        "Rating": 4.5
      },
      {
        "@search.action": "upload",
        "HotelId": "2",
        "HotelName": "Twin Dome Motel",
        "Description": "A classic hotel with modern amenities in the heart of downtown.",
        "Category": "Budget",
        "Tags": ["free wifi", "parking"],
        "Rating": 3.8
      }
    ]
  }'
```

**Example - Merge Document (partial update):**

```bash
dotnet run ../rest-http-caller/HttpCaller.cs -- POST "https://YOUR_SERVICE.search.windows.net/indexes('hotels-sample')/docs/index?api-version=2025-11-01-preview" \
  --header "Content-Type: application/json" \
  --header "api-key: YOUR_ADMIN_KEY" \
  --body '{
    "value": [
      {
        "@search.action": "merge",
        "HotelId": "1",
        "Rating": 4.8
      }
    ]
  }'
```

**Example - Delete Document:**

```bash
dotnet run ../rest-http-caller/HttpCaller.cs -- POST "https://YOUR_SERVICE.search.windows.net/indexes('hotels-sample')/docs/index?api-version=2025-11-01-preview" \
  --header "Content-Type: application/json" \
  --header "api-key: YOUR_ADMIN_KEY" \
  --body '{
    "value": [
      {
        "@search.action": "delete",
        "HotelId": "2"
      }
    ]
  }'
```

### 2.2 Get Document by Key

**Endpoint:**
```
GET https://[service-name].search.windows.net/indexes('{indexName}')/docs('{key}')?api-version=2025-11-01-preview
```

**Example:**
```bash
dotnet run ../rest-http-caller/HttpCaller.cs -- GET "https://YOUR_SERVICE.search.windows.net/indexes('hotels-sample')/docs('1')?api-version=2025-11-01-preview" \
  --header "api-key: YOUR_QUERY_KEY"
```

### 2.3 Count Documents

**Endpoint:**
```
GET https://[service-name].search.windows.net/indexes('{indexName}')/docs/$count?api-version=2025-11-01-preview
```

**Example:**
```bash
dotnet run ../rest-http-caller/HttpCaller.cs -- GET "https://YOUR_SERVICE.search.windows.net/indexes('hotels-sample')/docs/\$count?api-version=2025-11-01-preview" \
  --header "api-key: YOUR_QUERY_KEY"
```

---

## Part 3: Search Operations

### 3.1 Full-Text Search

**Endpoint (GET):**
```
GET https://[service-name].search.windows.net/indexes('{indexName}')/docs?search={query}&api-version=2025-11-01-preview
```

**Endpoint (POST - recommended for complex queries):**
```
POST https://[service-name].search.windows.net/indexes('{indexName}')/docs/search?api-version=2025-11-01-preview
```

**Search Parameters:**
| Parameter | Description |
|-----------|-------------|
| `search` | Search query text (use `*` for all documents) |
| `$filter` | OData filter expression |
| `$orderby` | Sort order |
| `$select` | Fields to return |
| `$top` | Number of results to return |
| `$skip` | Number of results to skip (pagination) |
| `$count` | Include total count of matches |
| `searchFields` | Limit search to specific fields |
| `searchMode` | `any` (default) or `all` for term matching |
| `queryType` | `simple` (default), `full` (Lucene syntax), or `semantic` |
| `facets` | Fields for faceted navigation |
| `highlight` | Fields for hit highlighting |

**Example - Simple Search:**

```bash
dotnet run ../rest-http-caller/HttpCaller.cs -- POST "https://YOUR_SERVICE.search.windows.net/indexes('hotels-sample')/docs/search?api-version=2025-11-01-preview" \
  --header "Content-Type: application/json" \
  --header "api-key: YOUR_QUERY_KEY" \
  --body '{
    "search": "pool",
    "select": "HotelId, HotelName, Description, Rating",
    "top": 10,
    "count": true
  }'
```

**Example - Filtered Search with Facets:**

```bash
dotnet run ../rest-http-caller/HttpCaller.cs -- POST "https://YOUR_SERVICE.search.windows.net/indexes('hotels-sample')/docs/search?api-version=2025-11-01-preview" \
  --header "Content-Type: application/json" \
  --header "api-key: YOUR_QUERY_KEY" \
  --body '{
    "search": "*",
    "filter": "Rating ge 4.0 and Category eq '\''Boutique'\''",
    "orderby": "Rating desc",
    "select": "HotelId, HotelName, Rating, Category",
    "facets": ["Category", "Tags"],
    "top": 20,
    "count": true
  }'
```

**Example - Full Lucene Query Syntax:**

```bash
dotnet run ../rest-http-caller/HttpCaller.cs -- POST "https://YOUR_SERVICE.search.windows.net/indexes('hotels-sample')/docs/search?api-version=2025-11-01-preview" \
  --header "Content-Type: application/json" \
  --header "api-key: YOUR_QUERY_KEY" \
  --body '{
    "search": "hotel AND (pool OR spa) -motel",
    "queryType": "full",
    "searchMode": "all",
    "top": 10
  }'
```

### 3.2 Vector Search

**Example - Pure Vector Search:**

```bash
dotnet run ../rest-http-caller/HttpCaller.cs -- POST "https://YOUR_SERVICE.search.windows.net/indexes('vector-sample')/docs/search?api-version=2025-11-01-preview" \
  --header "Content-Type: application/json" \
  --header "api-key: YOUR_QUERY_KEY" \
  --body '{
    "vectorQueries": [
      {
        "kind": "vector",
        "vector": [0.1, 0.2, ...],
        "fields": "contentVector",
        "k": 5
      }
    ],
    "select": "id, title, content"
  }'
```

**Example - Hybrid Search (Text + Vector):**

```bash
dotnet run ../rest-http-caller/HttpCaller.cs -- POST "https://YOUR_SERVICE.search.windows.net/indexes('vector-sample')/docs/search?api-version=2025-11-01-preview" \
  --header "Content-Type: application/json" \
  --header "api-key: YOUR_QUERY_KEY" \
  --body '{
    "search": "modern architecture",
    "vectorQueries": [
      {
        "kind": "vector",
        "vector": [0.1, 0.2, ...],
        "fields": "contentVector",
        "k": 5
      }
    ],
    "select": "id, title, content",
    "top": 10
  }'
```

### 3.3 Semantic Search

**Example - Semantic Search:**

```bash
dotnet run ../rest-http-caller/HttpCaller.cs -- POST "https://YOUR_SERVICE.search.windows.net/indexes('hotels-sample')/docs/search?api-version=2025-11-01-preview" \
  --header "Content-Type: application/json" \
  --header "api-key: YOUR_QUERY_KEY" \
  --body '{
    "search": "find hotels with great amenities for business travelers",
    "queryType": "semantic",
    "semanticConfiguration": "my-semantic-config",
    "captions": "extractive",
    "answers": "extractive",
    "top": 5
  }'
```

---

## Part 4: Agentic Retrieval (Knowledge Bases & Sources)

Agentic retrieval is a multi-query pipeline for complex agent-to-agent workflows. It targets a knowledge base that orchestrates retrieval from multiple knowledge sources with LLM-assisted query planning.

### 4.1 Knowledge Sources

Knowledge sources define where data comes from. Types include:
- `searchIndex` - Azure Search index
- `azureBlob` - Azure Blob Storage (with auto-ingestion)
- `indexedSharePoint` - Indexed SharePoint content
- `indexedOneLake` - Indexed OneLake content
- `web` - Web content
- `remoteSharePoint` - Live SharePoint queries

#### Create Knowledge Source (Search Index Type)

**Endpoint:**
```
PUT https://[service-name].search.windows.net/knowledgesources('{sourceName}')?api-version=2025-11-01-preview
```

**Example - Create Search Index Knowledge Source:**

```bash
dotnet run ../rest-http-caller/HttpCaller.cs -- PUT "https://YOUR_SERVICE.search.windows.net/knowledgesources('ks-hotels')?api-version=2025-11-01-preview" \
  --header "Content-Type: application/json" \
  --header "api-key: YOUR_ADMIN_KEY" \
  --header "Accept: application/json;odata.metadata=minimal" \
  --header "Prefer: return=representation" \
  --body '{
    "name": "ks-hotels",
    "kind": "searchIndex",
    "description": "Knowledge source for hotel information",
    "searchIndexParameters": {
      "searchIndexName": "hotels-sample",
      "sourceDataFields": [
        {"name": "Description"},
        {"name": "HotelName"},
        {"name": "Category"}
      ],
      "searchFields": [
        {"name": "*"}
      ],
      "semanticConfigurationName": "hotels-semantic-config"
    }
  }'
```

**Example - Create Azure Blob Knowledge Source:**

```bash
dotnet run ../rest-http-caller/HttpCaller.cs -- PUT "https://YOUR_SERVICE.search.windows.net/knowledgesources('ks-documents')?api-version=2025-11-01-preview" \
  --header "Content-Type: application/json" \
  --header "api-key: YOUR_ADMIN_KEY" \
  --header "Accept: application/json;odata.metadata=minimal" \
  --header "Prefer: return=representation" \
  --body '{
    "name": "ks-documents",
    "kind": "azureBlob",
    "description": "Knowledge source for document storage",
    "azureBlobParameters": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=YOUR_ACCOUNT;AccountKey=YOUR_KEY;EndpointSuffix=core.windows.net",
      "containerName": "documents",
      "folderPath": "knowledge-docs",
      "ingestionParameters": {
        "embeddingModel": {
          "kind": "azureOpenAI",
          "azureOpenAIParameters": {
            "resourceUri": "https://YOUR_OPENAI.openai.azure.com/",
            "deploymentId": "text-embedding-3-large",
            "apiKey": "YOUR_OPENAI_KEY",
            "modelName": "text-embedding-3-large"
          }
        },
        "chatCompletionModel": {
          "kind": "azureOpenAI",
          "azureOpenAIParameters": {
            "resourceUri": "https://YOUR_OPENAI.openai.azure.com/",
            "deploymentId": "gpt-4o-mini",
            "apiKey": "YOUR_OPENAI_KEY",
            "modelName": "gpt-4o-mini"
          }
        }
      }
    }
  }'
```

**Example - Create Web Knowledge Source:**

```bash
dotnet run ../rest-http-caller/HttpCaller.cs -- PUT "https://YOUR_SERVICE.search.windows.net/knowledgesources('ks-web')?api-version=2025-11-01-preview" \
  --header "Content-Type: application/json" \
  --header "api-key: YOUR_ADMIN_KEY" \
  --header "Accept: application/json;odata.metadata=minimal" \
  --header "Prefer: return=representation" \
  --body '{
    "name": "ks-web",
    "kind": "web",
    "description": "Web knowledge source",
    "webParameters": {
      "domains": {
        "allowedDomains": [
          {"address": "docs.microsoft.com", "includeSubpages": true},
          {"address": "learn.microsoft.com", "includeSubpages": true}
        ],
        "blockedDomains": [
          {"address": "spam-site.com"}
        ]
      }
    }
  }'
```

#### List Knowledge Sources

```bash
dotnet run ../rest-http-caller/HttpCaller.cs -- GET "https://YOUR_SERVICE.search.windows.net/knowledgesources?api-version=2025-11-01-preview" \
  --header "api-key: YOUR_ADMIN_KEY"
```

#### Delete Knowledge Source

```bash
dotnet run ../rest-http-caller/HttpCaller.cs -- DELETE "https://YOUR_SERVICE.search.windows.net/knowledgesources('ks-hotels')?api-version=2025-11-01-preview" \
  --header "api-key: YOUR_ADMIN_KEY"
```

### 4.2 Knowledge Bases

A knowledge base combines one or more knowledge sources with an LLM for query planning and answer synthesis.

#### Create Knowledge Base

**Endpoint:**
```
PUT https://[service-name].search.windows.net/knowledgebases('{knowledgeBaseName}')?api-version=2025-11-01-preview
```

**Example:**

```bash
dotnet run ../rest-http-caller/HttpCaller.cs -- PUT "https://YOUR_SERVICE.search.windows.net/knowledgebases('travel-kb')?api-version=2025-11-01-preview" \
  --header "Content-Type: application/json" \
  --header "api-key: YOUR_ADMIN_KEY" \
  --header "Accept: application/json;odata.metadata=minimal" \
  --header "Prefer: return=representation" \
  --body '{
    "name": "travel-kb",
    "description": "Knowledge base for travel and hotel information",
    "knowledgeSources": [
      {"name": "ks-hotels"},
      {"name": "ks-documents"}
    ],
    "models": [
      {
        "kind": "azureOpenAI",
        "azureOpenAIParameters": {
          "resourceUri": "https://YOUR_OPENAI.openai.azure.com/",
          "deploymentId": "gpt-4o-mini",
          "apiKey": "YOUR_OPENAI_KEY",
          "modelName": "gpt-4o-mini"
        }
      }
    ],
    "retrievalReasoningEffort": {"kind": "low"},
    "outputMode": "answerSynthesis",
    "retrievalInstructions": "Focus on hotel amenities, location, and pricing when searching for hotels.",
    "answerInstructions": "Provide helpful, concise answers about travel destinations and accommodations."
  }'
```

**Reasoning Effort Levels:**
| Level | Description |
|-------|-------------|
| `minimal` | No source selection, query planning, or iterative search |
| `low` | Basic reasoning during retrieval |
| `medium` | Moderate reasoning with more iteration |

**Output Modes:**
| Mode | Description |
|------|-------------|
| `extractiveData` | Return raw data from knowledge sources |
| `answerSynthesis` | LLM synthesizes an answer from retrieved data |

#### List Knowledge Bases

```bash
dotnet run ../rest-http-caller/HttpCaller.cs -- GET "https://YOUR_SERVICE.search.windows.net/knowledgebases?api-version=2025-11-01-preview" \
  --header "api-key: YOUR_ADMIN_KEY"
```

#### Delete Knowledge Base

```bash
dotnet run ../rest-http-caller/HttpCaller.cs -- DELETE "https://YOUR_SERVICE.search.windows.net/knowledgebases('travel-kb')?api-version=2025-11-01-preview" \
  --header "api-key: YOUR_ADMIN_KEY"
```

### 4.3 Knowledge Retrieval

Execute retrieval queries against a knowledge base.

**Endpoint:**
```
POST https://[service-name].search.windows.net/knowledgebases('{knowledgeBaseName}')/retrieve?api-version=2025-11-01-preview
```

**Example - Chat-Style Retrieval:**

```bash
dotnet run ../rest-http-caller/HttpCaller.cs -- POST "https://YOUR_SERVICE.search.windows.net/knowledgebases('travel-kb')/retrieve?api-version=2025-11-01-preview" \
  --header "Content-Type: application/json" \
  --header "api-key: YOUR_ADMIN_KEY" \
  --header "Accept: application/json;odata.metadata=minimal" \
  --body '{
    "messages": [
      {
        "role": "user",
        "content": [
          {"type": "text", "text": "Find me a boutique hotel with a pool and good ratings"}
        ]
      }
    ],
    "maxRuntimeInSeconds": 60,
    "maxOutputSize": 50000,
    "retrievalReasoningEffort": {"kind": "low"},
    "outputMode": "answerSynthesis",
    "includeActivity": true,
    "knowledgeSourceParams": [
      {
        "kind": "searchIndex",
        "knowledgeSourceName": "ks-hotels",
        "includeReferences": true,
        "includeReferenceSourceData": true,
        "rerankerThreshold": 2.0
      }
    ]
  }'
```

**Example - Intent-Based Retrieval (Skip Query Planning):**

```bash
dotnet run ../rest-http-caller/HttpCaller.cs -- POST "https://YOUR_SERVICE.search.windows.net/knowledgebases('travel-kb')/retrieve?api-version=2025-11-01-preview" \
  --header "Content-Type: application/json" \
  --header "api-key: YOUR_ADMIN_KEY" \
  --header "Accept: application/json;odata.metadata=minimal" \
  --body '{
    "intents": [
      {"type": "semantic", "search": "boutique hotels with pool amenities"}
    ],
    "maxRuntimeInSeconds": 30,
    "retrievalReasoningEffort": {"kind": "minimal"},
    "outputMode": "extractiveData",
    "includeActivity": true
  }'
```

**Response Structure:**

The retrieval response contains:
- `response` - The synthesized answer or extracted data
- `activity` - Activity records showing what operations were performed (query planning, searches, reasoning)
- `references` - Document references with source data and reranker scores

---

## Part 5: Common Workflows

### Workflow 1: Create Index, Upload Documents, and Search

```bash
# 1. Get authentication token
$TOKEN = az account get-access-token --resource https://search.azure.com --query accessToken --output tsv

# 2. Create the index
dotnet run ../rest-http-caller/HttpCaller.cs -- PUT "https://YOUR_SERVICE.search.windows.net/indexes('products')?api-version=2025-11-01-preview" \
  --header "Authorization: Bearer $TOKEN" \
  --header "Content-Type: application/json" \
  --header "Prefer: return=representation" \
  --body '{"name": "products", "fields": [{"name": "id", "type": "Edm.String", "key": true}, {"name": "name", "type": "Edm.String", "searchable": true}, {"name": "description", "type": "Edm.String", "searchable": true}, {"name": "price", "type": "Edm.Double", "filterable": true, "sortable": true}]}'

# 3. Upload documents
dotnet run ../rest-http-caller/HttpCaller.cs -- POST "https://YOUR_SERVICE.search.windows.net/indexes('products')/docs/index?api-version=2025-11-01-preview" \
  --header "Authorization: Bearer $TOKEN" \
  --header "Content-Type: application/json" \
  --body '{"value": [{"@search.action": "upload", "id": "1", "name": "Widget", "description": "A useful widget", "price": 9.99}]}'

# 4. Search the index
dotnet run ../rest-http-caller/HttpCaller.cs -- POST "https://YOUR_SERVICE.search.windows.net/indexes('products')/docs/search?api-version=2025-11-01-preview" \
  --header "Authorization: Bearer $TOKEN" \
  --header "Content-Type: application/json" \
  --body '{"search": "widget", "top": 10}'
```

### Workflow 2: Set Up Agentic Retrieval

```bash
# 1. Create a knowledge source pointing to your index
dotnet run ../rest-http-caller/HttpCaller.cs -- PUT "https://YOUR_SERVICE.search.windows.net/knowledgesources('ks-products')?api-version=2025-11-01-preview" \
  --header "api-key: YOUR_ADMIN_KEY" \
  --header "Content-Type: application/json" \
  --header "Prefer: return=representation" \
  --body '{"name": "ks-products", "kind": "searchIndex", "searchIndexParameters": {"searchIndexName": "products", "sourceDataFields": [{"name": "name"}, {"name": "description"}], "searchFields": [{"name": "*"}]}}'

# 2. Create a knowledge base with the source and an LLM
dotnet run ../rest-http-caller/HttpCaller.cs -- PUT "https://YOUR_SERVICE.search.windows.net/knowledgebases('products-kb')?api-version=2025-11-01-preview" \
  --header "api-key: YOUR_ADMIN_KEY" \
  --header "Content-Type: application/json" \
  --header "Prefer: return=representation" \
  --body '{"name": "products-kb", "knowledgeSources": [{"name": "ks-products"}], "models": [{"kind": "azureOpenAI", "azureOpenAIParameters": {"resourceUri": "https://YOUR_OPENAI.openai.azure.com/", "deploymentId": "gpt-4o-mini", "apiKey": "YOUR_OPENAI_KEY", "modelName": "gpt-4o-mini"}}], "retrievalReasoningEffort": {"kind": "low"}, "outputMode": "answerSynthesis"}'

# 3. Query the knowledge base
dotnet run ../rest-http-caller/HttpCaller.cs -- POST "https://YOUR_SERVICE.search.windows.net/knowledgebases('products-kb')/retrieve?api-version=2025-11-01-preview" \
  --header "api-key: YOUR_ADMIN_KEY" \
  --header "Content-Type: application/json" \
  --body '{"messages": [{"role": "user", "content": [{"type": "text", "text": "What products do you have under $10?"}]}], "includeActivity": true}'
```

---

## Error Handling

| HTTP Status | Meaning | Resolution |
|-------------|---------|------------|
| 400 | Bad Request | Check request body syntax and field names |
| 401 | Unauthorized | Verify API key or bearer token |
| 403 | Forbidden | Check permissions (admin vs query key) |
| 404 | Not Found | Verify index/resource name exists |
| 409 | Conflict | Resource already exists with different definition |
| 429 | Too Many Requests | Reduce request rate, implement backoff |
| 503 | Service Unavailable | Retry with exponential backoff |

---

## Tips for LLM/Agent Usage

1. **Use Bearer tokens** for automated workflows - tokens expire, so refresh them periodically
2. **Batch document operations** - upload up to 1000 docs or 16 MB per request
3. **Use POST for searches** - easier to construct complex queries with JSON body
4. **Enable `includeActivity`** in retrieval requests to understand what operations were performed
5. **Set appropriate `rerankerThreshold`** to filter low-quality matches
6. **Choose reasoning effort wisely** - `minimal` for simple queries, `low`/`medium` for complex multi-source queries
7. **Always include `api-version`** parameter in all requests

## Reference Links

- [Azure AI Search Documentation](https://learn.microsoft.com/en-us/azure/search/)
- [REST API Reference](https://learn.microsoft.com/en-us/rest/api/searchservice/)
- [Agentic Retrieval Overview](https://learn.microsoft.com/en-us/azure/search/agentic-retrieval-overview)
- [Knowledge Base Guide](https://learn.microsoft.com/en-us/azure/search/agentic-retrieval-how-to-create-knowledge-base)
- [Knowledge Source Overview](https://learn.microsoft.com/en-us/azure/search/agentic-knowledge-source-overview)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mattgotteiner) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
