---
name: api-usage
description: How to use the OpenAI-compatible API server Use when this capability is needed.
metadata:
  author: felipepimentel
---

# OpenAI API Usage Skill

The `kor-plugin-openai-api` exposes KOR agents via an OpenAI-compatible REST API.

## Starting the Server

```bash
kor serve --port 8080
```

## API Endpoints

### Chat Completions

```
POST /v1/chat/completions
```

Request body:

```json
{
  "model": "kor-supervisor",
  "messages": [
    {"role": "user", "content": "Hello, KOR!"}
  ]
}
```

### List Models

```
GET /v1/models
```

Lists available agents as "models".

## Integration

Works with any OpenAI SDK:

```python
from openai import OpenAI

client = OpenAI(base_url="http://localhost:8080/v1")
response = client.chat.completions.create(
    model="kor-supervisor",
    messages=[{"role": "user", "content": "Hello!"}]
)
```

## Use Cases

- IDE integrations (Cursor, Continue, etc.)
- Custom applications via REST
- Testing and development

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/felipepimentel) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
