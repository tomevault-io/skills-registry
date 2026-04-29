---
name: pinecone
description: Manage Pinecone vector database — indexes, upsert vectors, query, and delete via the REST API. Use when this capability is needed.
metadata:
  author: thinkfleetai
---

# Pinecone

Manage vector indexes, upsert and query vectors.

## Environment Variables

- `PINECONE_API_KEY` - API key
- `PINECONE_INDEX_URL` - Index URL (e.g. `https://my-index-abc123.svc.pinecone.io`)

## List indexes

```bash
curl -s -H "Api-Key: $PINECONE_API_KEY" \
  "https://api.pinecone.io/indexes" | jq '.indexes[] | {name, dimension, metric, status: .status.state}'
```

## Query vectors

```bash
curl -s -X POST -H "Api-Key: $PINECONE_API_KEY" \
  -H "Content-Type: application/json" \
  "$PINECONE_INDEX_URL/query" \
  -d '{"vector":[0.1,0.2,0.3],"topK":5,"includeMetadata":true}' | jq '.matches[] | {id, score, metadata}'
```

## Upsert vectors

```bash
curl -s -X POST -H "Api-Key: $PINECONE_API_KEY" \
  -H "Content-Type: application/json" \
  "$PINECONE_INDEX_URL/vectors/upsert" \
  -d '{"vectors":[{"id":"vec1","values":[0.1,0.2,0.3],"metadata":{"text":"example"}}]}' | jq '{upsertedCount}'
```

## Describe index stats

```bash
curl -s -X POST -H "Api-Key: $PINECONE_API_KEY" \
  "$PINECONE_INDEX_URL/describe_index_stats" | jq '{dimension, totalVectorCount}'
```

## Delete vectors

```bash
curl -s -X POST -H "Api-Key: $PINECONE_API_KEY" \
  -H "Content-Type: application/json" \
  "$PINECONE_INDEX_URL/vectors/delete" \
  -d '{"ids":["vec1","vec2"]}' | jq '.'
```

## Notes

- Always confirm before upserting or deleting vectors.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/thinkfleetai) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
