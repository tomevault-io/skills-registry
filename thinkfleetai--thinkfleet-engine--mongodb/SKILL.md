---
name: mongodb
description: Query MongoDB Atlas — collections, documents, and aggregations via the Data API. Use when this capability is needed.
metadata:
  author: thinkfleetai
---

# MongoDB Atlas

Query collections and documents via the MongoDB Atlas Data API.

## Environment Variables

- `MONGODB_URI` - MongoDB connection URI or Atlas Data API key

## Find documents

```bash
curl -s -X POST -H "Content-Type: application/json" \
  -H "api-key: $MONGODB_URI" \
  "https://data.mongodb-api.com/app/data-xxx/endpoint/data/v1/action/find" \
  -d '{"dataSource":"Cluster0","database":"mydb","collection":"mycoll","filter":{},"limit":10}' | jq '.documents[]'
```

## Insert document

```bash
curl -s -X POST -H "Content-Type: application/json" \
  -H "api-key: $MONGODB_URI" \
  "https://data.mongodb-api.com/app/data-xxx/endpoint/data/v1/action/insertOne" \
  -d '{"dataSource":"Cluster0","database":"mydb","collection":"mycoll","document":{"name":"test","value":42}}' | jq '{insertedId}'
```

## Aggregate

```bash
curl -s -X POST -H "Content-Type: application/json" \
  -H "api-key: $MONGODB_URI" \
  "https://data.mongodb-api.com/app/data-xxx/endpoint/data/v1/action/aggregate" \
  -d '{"dataSource":"Cluster0","database":"mydb","collection":"mycoll","pipeline":[{"$group":{"_id":"$field","count":{"$sum":1}}}]}' | jq '.documents[]'
```

## Notes

- Replace `data-xxx` with your Atlas app ID.
- Always confirm before inserting or deleting documents.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/thinkfleetai) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
