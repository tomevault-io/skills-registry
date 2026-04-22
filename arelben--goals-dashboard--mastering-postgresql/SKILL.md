---
name: mastering-postgresql
description: > Use when this capability is needed.
metadata:
  author: arelben
---

# PostgreSQL Python Development

Build search, vector similarity, and data-intensive applications with PostgreSQL and Python.

## Quick Reference

| Task | Go To |
|------|-------|
| Docker/local setup | [setup-and-docker.md](references/setup-and-docker.md) |
| Full-text search & BM25 | [search-fulltext.md](references/search-fulltext.md) |
| pgvector & JSONB indexing | [search-vectors-json.md](references/search-vectors-json.md) |
| Python drivers & pools | [python-drivers.md](references/python-drivers.md) |
| Python query patterns | [python-queries.md](references/python-queries.md) |
| AWS RDS/Aurora | [cloud-aws.md](references/cloud-aws.md) |
| GCP Cloud SQL/AlloyDB | [cloud-gcp.md](references/cloud-gcp.md) |
| Azure Flexible Server | [cloud-azure.md](references/cloud-azure.md) |
| Neon & Supabase | [cloud-serverless.md](references/cloud-serverless.md) |
| Cloud common (pooling, config) | [cloud-common.md](references/cloud-common.md) |

## When NOT to Use This Skill

- **DBA tasks:** Backup strategies, replication setup, user management, security hardening
- **Other databases:** MySQL, MongoDB, Redis, Elasticsearch-specific queries
- **Schema design:** Normalization theory, data modeling patterns
- **Stored procedures:** PL/pgSQL function development
- **Application frameworks:** Django ORM specifics, FastAPI integration details

## Quick Start Checklist

Copy this checklist to track progress:

```
Setup Progress:
- [ ] Docker environment running (docker-compose up -d)
- [ ] Connected to database (psql or Python)
- [ ] Extensions created (pgvector, pg_trgm)
- [ ] Table created with search_vector and embedding columns
- [ ] GIN index on search_vector created
- [ ] HNSW index on embedding created
- [ ] Test full-text query returns results
- [ ] Test vector query returns results
```

## Quick Start: Search + Vectors in 5 Minutes

### 1. Start PostgreSQL with pgvector

```yaml
# docker-compose.yml
services:
  postgres:
    image: pgvector/pgvector:pg17
    environment:
      POSTGRES_PASSWORD: devpass
    ports: ["5432:5432"]
    volumes: [pgdata:/var/lib/postgresql/data]
volumes:
  pgdata:
```

```bash
docker-compose up -d

# Verify container is running:
docker-compose ps
# Expected: postgres service with status "Up"
```

### 2. Enable Extensions

```sql
CREATE EXTENSION vector;      -- pgvector for embeddings
CREATE EXTENSION pg_trgm;     -- Trigram for fuzzy search

-- Verify extensions installed:
SELECT extname, extversion FROM pg_extension 
WHERE extname IN ('vector', 'pg_trgm');
-- Expected: 2 rows with version numbers
```

### 3. Create Searchable Table with Vectors

```sql
CREATE TABLE documents (
    id BIGSERIAL PRIMARY KEY,
    title TEXT NOT NULL,
    content TEXT,
    embedding vector(1536),
    search_vector tsvector GENERATED ALWAYS AS (
        setweight(to_tsvector('english', coalesce(title, '')), 'A') ||
        setweight(to_tsvector('english', coalesce(content, '')), 'B')
    ) STORED
);

-- Create indexes
CREATE INDEX idx_docs_search ON documents USING GIN (search_vector);
CREATE INDEX idx_docs_embedding ON documents USING hnsw (embedding vector_cosine_ops);

-- Verify indexes created:
SELECT indexname FROM pg_indexes WHERE tablename = 'documents';
-- Expected: idx_docs_search, idx_docs_embedding, documents_pkey
```

### 4. Query from Python

```python
import asyncpg

async def search(pool, query: str, embedding: list[float], limit: int = 10):
    return await pool.fetch("""
        SELECT id, title,
               ts_rank(search_vector, websearch_to_tsquery('english', $1)) AS text_rank,
               embedding <=> $2::vector AS vector_dist
        FROM documents
        WHERE search_vector @@ websearch_to_tsquery('english', $1)
        ORDER BY vector_dist
        LIMIT $3
    """, query, embedding, limit)

# Verify connection works:
# pool = await asyncpg.create_pool('postgresql://postgres:devpass@localhost/postgres')
# rows = await pool.fetch("SELECT 1 AS test")
# assert rows[0]['test'] == 1
```

## Decision Trees

### Which Search Approach?

```
Need search? ─┬─► Exact keyword match ──────► B-tree index + WHERE =
              │
              ├─► Full-text search (FTS) ───► tsvector + GIN + ts_rank
              │
              ├─► Relevance like Google ────► pg_search BM25 (ParadeDB)
              │
              ├─► Typo tolerance ───────────► pg_trgm + similarity()
              │
              ├─► Semantic/AI search ───────► pgvector + embeddings
              │
              └─► Hybrid (keywords + semantic) ► Combine tsvector + pgvector
```

### Which Vector Index?

```
Vector index? ─┬─► Dataset < 100K rows ────► No index (exact search OK)
               │
               ├─► Need best recall ────────► HNSW (slower build, fast query)
               │
               ├─► Fast index build ────────► IVFFlat (needs data first)
               │
               ├─► On AlloyDB ──────────────► ScaNN (Google optimized)
               │
               ├─► On Azure ────────────────► pg_diskann (32x less memory)
               │
               ├─► Billions of vectors ─────► VectorChord vchordrq (self-host)
               │
               └─► Dimensions > 2000 ───────► halfvec or binary quantization
```

### Which Python Library?

```
Python lib? ──┬─► Sync, simple, stable ─────► psycopg2
              │
              ├─► Async + modern features ──► psycopg3
              │
              ├─► Max async performance ────► asyncpg
              │
              └─► ORM needed ───────────────► SQLAlchemy + asyncpg/psycopg
```

### Which Index Type for Column?

```
Column type? ─┬─► Scalar (int, text, timestamp) ─► B-tree (default)
              │
              ├─► JSONB ────────────────────────┬► GIN (general queries)
              │                                 └► GIN jsonb_path_ops (@> only)
              │
              ├─► Array ────────────────────────► GIN
              │
              ├─► tsvector ─────────────────────► GIN (or GiST for updates)
              │
              ├─► vector ───────────────────────► HNSW or IVFFlat
              │
              └─► Range / Geometric ────────────► GiST
```

## Common Patterns

For implementation details, see the reference files:

| Pattern | Reference |
|---------|-----------|
| Full-text search with ranking | [search-fulltext.md#ranking-functions](references/search-fulltext.md#ranking-functions) |
| BM25 search | [search-fulltext.md#bm25-with-pg_search](references/search-fulltext.md#bm25-with-pg_search) |
| Vector similarity | [search-vectors-json.md#distance-operators](references/search-vectors-json.md#distance-operators) |
| JSONB containment | [search-vectors-json.md#jsonb-indexing](references/search-vectors-json.md#jsonb-indexing) |
| Array overlap | [search-vectors-json.md#array-indexing](references/search-vectors-json.md#array-indexing) |
| Bulk insert | [python-queries.md#bulk-insert-strategies](references/python-queries.md#bulk-insert-strategies) |
| Connection pool | [python-drivers.md#asyncpg-async-only](references/python-drivers.md#asyncpg-async-only) |

## Index Tuning Quick Reference

### HNSW Parameters

| Parameter | Default | Guidance |
|-----------|---------|----------|
| `m` | 16 | Higher = better recall, more memory. 12-48 typical |
| `ef_construction` | 64 | Higher = better index quality, slower build. 64-200 |
| `hnsw.ef_search` | 40 | Set at query time. Higher = better recall, slower |

```sql
CREATE INDEX ON docs USING hnsw (embedding vector_cosine_ops) WITH (m=16, ef_construction=100);
SET hnsw.ef_search = 100;  -- Before querying

-- Verify setting applied:
SHOW hnsw.ef_search;
```

### IVFFlat Parameters

| Parameter | Guidance |
|-----------|----------|
| `lists` | sqrt(rows) for <1M rows; rows/1000 for >1M |
| `ivfflat.probes` | Start at sqrt(lists), increase for recall |

```sql
CREATE INDEX ON docs USING ivfflat (embedding vector_l2_ops) WITH (lists=100);
SET ivfflat.probes = 10;
```

## Troubleshooting Quick Reference

| Symptom | Likely Cause | Fix |
|---------|--------------|-----|
| Seq Scan on indexed column | Stats outdated | `ANALYZE tablename;` |
| Vector search slow | No index or low ef_search | Create HNSW index, increase ef_search |
| Poor vector recall | IVFFlat probes too low | Increase `ivfflat.probes` |
| FTS not matching | Wrong language config | Check `to_tsvector('english', ...)` |
| Index not used | Query doesn't match ops | Verify operator class matches query |
| Connection timeout | Pool exhausted | Increase pool size or fix leaks |
| Extension not found | Not installed | `CREATE EXTENSION name;` |
| HNSW build OOM | Insufficient memory | Increase `maintenance_work_mem` |
| Filtered queries return few results | Filtering after index scan | Enable `hnsw.iterative_scan` |
| Connection drops in production | No health checking | Use `check=ConnectionPool.check_connection` |
| Scaling past 100M vectors | pgvector limits | Consider VectorChord vchordrq |

For detailed troubleshooting, see [search-vectors-json.md](references/search-vectors-json.md#troubleshooting).

## Script Usage

```bash
pip install -r scripts/requirements.txt  # Install dependencies first
```

| Script | Purpose | When to Use |
|--------|---------|-------------|
| `setup_extensions.py` | Install pgvector, pg_trgm extensions | Initial database setup |
| `create_search_tables.py` | Create tables with search_vector and embedding columns | After extensions installed |
| `health_check.py` | Check index health, bloat, and performance | Diagnosing slow queries |
| `vector_search.py --demo` | Demonstrate vector similarity queries | Learning pgvector patterns |
| `bulk_insert.py` | High-performance data loading | Importing large datasets |
| `fts_examples.py` | Full-text search query examples | Learning FTS syntax |
| `connection_pool.py` | Connection pooling patterns | Production deployments |

**Example:**
```bash
python scripts/setup_extensions.py --host localhost --dbname mydb
python scripts/create_search_tables.py --host localhost --dbname mydb
python scripts/health_check.py --host localhost --dbname mydb
```

## Cloud Quick Reference

| Provider | pgvector | BM25 Support | Connection Pooling |
|----------|----------|--------------|-------------------|
| AWS RDS/Aurora | 0.8.0 | pg_textsearch (preview) | RDS Proxy |
| GCP Cloud SQL | 0.8.0 | pg_textsearch (preview) | Cloud SQL Proxy |
| GCP AlloyDB | 0.8.0 + ScaNN | pg_textsearch (preview) | Built-in |
| Azure Flexible | 0.8.0 + pg_diskann | pg_textsearch (preview) | Built-in PgBouncer |
| Neon | ✅ | pg_search | Built-in |
| Supabase | ✅ | pg_search | Built-in |

**Serverless options:** Neon (scale-to-zero, instant branching) and Supabase (BaaS with auth/real-time) are ideal for dev/test and startups. See [cloud-serverless.md](references/cloud-serverless.md).

**BM25 Options:**
- **pg_search (ParadeDB)**: Production-ready, self-host or ParadeDB managed service
- **pg_textsearch (TigerData)**: Preview status, available on managed PostgreSQL services

See provider-specific files for setup commands: [AWS](references/cloud-aws.md) | [GCP](references/cloud-gcp.md) | [Azure](references/cloud-azure.md)

## Reference Files

Load these for detailed implementation guidance:

| Reference | Load When |
|-----------|-----------|
| [setup-and-docker.md](references/setup-and-docker.md) | Docker setup, extension installation, postgresql.conf tuning |
| [search-fulltext.md](references/search-fulltext.md) | Full-text search (FTS), BM25 setup, trigram fuzzy search |
| [search-vectors-json.md](references/search-vectors-json.md) | pgvector tuning, JSONB/array indexing, maintenance |
| [python-drivers.md](references/python-drivers.md) | psycopg2/psycopg3/asyncpg, connection pools, SQLAlchemy |
| [python-queries.md](references/python-queries.md) | Bulk inserts, FTS queries, vector queries, JSONB operations |
| [cloud-aws.md](references/cloud-aws.md) | AWS RDS/Aurora setup, RDS Proxy |
| [cloud-gcp.md](references/cloud-gcp.md) | GCP Cloud SQL/AlloyDB, ScaNN indexes |
| [cloud-azure.md](references/cloud-azure.md) | Azure Flexible Server, pg_diskann |
| [cloud-serverless.md](references/cloud-serverless.md) | Neon, Supabase (scale-to-zero, branching) |
| [cloud-common.md](references/cloud-common.md) | Extension matrix, pooling, production config, costs |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/arelben) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
