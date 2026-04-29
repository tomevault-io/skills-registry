---
name: weaviate
description: Manage Weaviate vector database — classes, objects, and vector search via the REST API. Use when this capability is needed.
metadata:
  author: thinkfleetai
---

# Weaviate

Manage classes, objects, and vector search.

## Environment Variables

- `WEAVIATE_URL` - Weaviate instance URL
- `WEAVIATE_API_KEY` - API key

## Get schema

```bash
curl -s -H "Authorization: Bearer $WEAVIATE_API_KEY" \
  "$WEAVIATE_URL/v1/schema" | jq '.classes[] | {class, properties: [.properties[] | .name]}'
```

## List objects

```bash
curl -s -H "Authorization: Bearer $WEAVIATE_API_KEY" \
  "$WEAVIATE_URL/v1/objects?limit=10&class=ClassName" | jq '.objects[] | {id, properties}'
```

## GraphQL vector search

```bash
curl -s -X POST -H "Authorization: Bearer $WEAVIATE_API_KEY" \
  -H "Content-Type: application/json" \
  "$WEAVIATE_URL/v1/graphql" \
  -d '{"query":"{Get{ClassName(nearText:{concepts:[\"search query\"]}limit:5){property1 _additional{distance}}}}"}' | jq '.data.Get.ClassName[]'
```

## Add object

```bash
curl -s -X POST -H "Authorization: Bearer $WEAVIATE_API_KEY" \
  -H "Content-Type: application/json" \
  "$WEAVIATE_URL/v1/objects" \
  -d '{"class":"ClassName","properties":{"property1":"value1"}}' | jq '{id}'
```

## Notes

- Always confirm before adding or deleting objects.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/thinkfleetai) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
