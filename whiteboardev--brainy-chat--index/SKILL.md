---
name: index
description: Index codebases for semantic code search. Use this skill to make a project's source code searchable via vector similarity, enabling natural language queries like "how does authentication work" or "find the database connection logic". Use when this capability is needed.
metadata:
  author: whiteboardev
---

# Index Skill

Index codebases for semantic code search via BrAIny API. Enables natural language queries over source code using vector embeddings.

## Quick Start

```bash
# Index a codebase
bun scripts/index.ts index /path/to/project --name "my-project"

# Search indexed code
bun scripts/index.ts search "authentication middleware" --limit 10

# Reindex after changes
bun scripts/index.ts reindex

# List indexed codebases
bun scripts/index.ts list
```

## Environment Variables

| Variable | Purpose | Default |
|----------|---------|---------|
| `BRAINY_URL` | API base URL | `http://localhost:3090` |
| `BRAINY_PROJECT_ID` | Project ID for organization | Required |

## API Reference

### Index a Codebase

```http
POST /v1/projects/{projectId}/codebases
Content-Type: application/json

{
  "name": "brainy",
  "path": "/Users/jp/Projects/brainy"
}
```

**Response**

```json
{
  "success": true,
  "codebaseId": "uuid",
  "stats": {
    "totalFiles": 52,
    "newFiles": 52,
    "modifiedFiles": 0,
    "deletedFiles": 0,
    "totalChunks": 215,
    "indexedChunks": 215
  }
}
```

### Reindex a Codebase

Performs incremental update - only processes new/modified files:

```http
POST /v1/codebases/{codebaseId}/reindex
```

**Response**

```json
{
  "success": true,
  "codebaseId": "uuid",
  "stats": {
    "totalFiles": 54,
    "newFiles": 2,
    "modifiedFiles": 3,
    "deletedFiles": 0,
    "totalChunks": 220,
    "indexedChunks": 220
  }
}
```

### Search Code

```http
GET /v1/codebases/{codebaseId}/search?query={text}&limit={n}
```

| Parameter | Description |
|-----------|-------------|
| `query` | Natural language search text |
| `limit` | Max results (default 10, max 50) |

**Response**

```json
{
  "success": true,
  "chunks": [
    {
      "id": "uuid",
      "filePath": "src/services/embedding.ts",
      "startLine": 1,
      "endLine": 56,
      "content": "import OpenAI from 'openai'...",
      "tokenCount": 428,
      "similarity": 0.42,
      "language": "typescript"
    }
  ],
  "totalCount": 20
}
```

### List Codebases

```http
GET /v1/projects/{projectId}/codebases
```

### Get Codebase Details

```http
GET /v1/codebases/{codebaseId}
```

### Delete Codebase

Soft deletes the codebase and all its chunks:

```http
DELETE /v1/codebases/{codebaseId}
```

## How Indexing Works

### 1. Crawling

The crawler walks the directory tree and discovers source files:

**Automatically Ignored**
- `.git`, `.svn`, `.hg` (version control)
- `node_modules`, `vendor` (dependencies)
- `dist`, `build`, `target`, `out` (build outputs)
- `.idea`, `.vscode` (IDE configs)
- `*.min.js`, `*.min.css` (minified files)
- Binary files (images, PDFs, archives, executables)
- Files > 5MB

**Language Detection**
Detected by extension: TypeScript, JavaScript, Python, Go, Rust, Java, Ruby, PHP, C/C++, SQL, HTML, CSS, JSON, YAML, Markdown, and more.

### 2. Chunking

Files are split into chunks optimized for embeddings:

| Parameter | Value | Purpose |
|-----------|-------|---------|
| Target tokens | ~500 | Optimal semantic density |
| Max tokens | 2000 | Safe buffer below model limit |
| Min tokens | 50 | Avoid tiny fragments |

**Semantic Boundaries**
Chunks break at natural points:
- Empty lines
- Function/class/interface definitions
- Export statements
- Comment blocks (documentation)

### 3. Embedding

Each chunk is embedded using OpenAI's `text-embedding-3-small` model (1536 dimensions). Embeddings enable semantic similarity search.

### 4. Storage

Chunks are stored in PostgreSQL with pgvector:

```sql
code_chunks (
  id UUID PRIMARY KEY,
  codebase_id UUID NOT NULL,
  file_id UUID,
  chunk_index INTEGER NOT NULL,
  content TEXT NOT NULL,
  start_line INTEGER,
  end_line INTEGER,
  token_count INTEGER NOT NULL,
  embedding vector(1536),
  deleted_at TIMESTAMPTZ,
  created_at TIMESTAMPTZ
)
```

**Indexes**
- HNSW on `embedding` for fast vector similarity (cosine distance)
- B-tree on `codebase_id`, `file_id` for filtering

## Incremental Updates

When reindexing, the system:

1. Compares file hashes to detect changes
2. Skips unchanged files
3. Re-chunks and re-embeds modified files
4. Removes chunks from deleted files
5. Adds chunks for new files

This makes reindexing fast after small changes.

## Search Tips

**Natural Language Queries**
- "How does authentication work"
- "Database connection pooling"
- "Error handling in API routes"
- "Token generation for embeddings"

**Specific Code Patterns**
- "PostgreSQL query with vector search"
- "Hono middleware for validation"
- "TypeScript interface for memory"

**Finding Implementations**
- "EmbeddingService class"
- "chunkByTokens function"
- "Memory repository insert"

## Database Schema

### codebases

```sql
codebases (
  id UUID PRIMARY KEY,
  project_id UUID NOT NULL,
  name TEXT NOT NULL,
  path TEXT NOT NULL,
  total_files INTEGER DEFAULT 0,
  total_chunks INTEGER DEFAULT 0,
  last_indexed_at TIMESTAMPTZ,
  deleted_at TIMESTAMPTZ,
  created_at TIMESTAMPTZ,
  updated_at TIMESTAMPTZ
)
```

### codebase_files

```sql
codebase_files (
  id UUID PRIMARY KEY,
  codebase_id UUID NOT NULL,
  file_path TEXT NOT NULL,
  file_hash TEXT NOT NULL,
  language TEXT,
  chunk_count INTEGER DEFAULT 0,
  last_indexed_at TIMESTAMPTZ,
  deleted_at TIMESTAMPTZ,
  created_at TIMESTAMPTZ
)
```

### code_chunks

```sql
code_chunks (
  id UUID PRIMARY KEY,
  codebase_id UUID NOT NULL,
  file_id UUID,
  chunk_index INTEGER NOT NULL,
  content TEXT NOT NULL,
  start_line INTEGER,
  end_line INTEGER,
  token_count INTEGER NOT NULL,
  embedding vector(1536),
  deleted_at TIMESTAMPTZ,
  created_at TIMESTAMPTZ
)
```

## Usage Guidelines

1. Index codebases at the start of a project for code-aware assistance
2. Reindex after significant changes (new features, refactors)
3. Use semantic search to understand unfamiliar codebases
4. Search for patterns when implementing similar functionality
5. Keep different projects in separate BrAIny projects for isolation

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/whiteboardev) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
